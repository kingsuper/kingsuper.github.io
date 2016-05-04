---
layout: post
title: Octavia中service虚拟机服务介绍
description: Octavia中service虚拟机服务介绍
category: blog
---

**声明：  
本博客欢迎转发，但请保留原作者信息!  
新浪微博：[@Lingxian_Kong](http://weibo.com/lingxiankong)   
博客地址：<http://lingxiankong.github.io/>  
联系邮箱：<anlin.kong@gmail.com>  
内容系本人学习、研究和总结，如有雷同，实属荣幸！**

在octavia中，实际的loadbalancer服务是由虚拟机提供，所以在虚拟机中需要运行服务与octavia通信，接收octavia发来的操作指令，配置网卡、路由以及haproxy和keepalived软件。

实际上，虚拟机里面会运行amphora-agent进程，对外提供REST API访问，如下所示：

    ubuntu@amphora-01742827-7aa1-4e2c-b2d9-6d4f1f200c79:~$ ps -ef | grep agent | grep -v grep
    root       186     1  0 Apr15 ?        00:00:35 /usr/bin/python /usr/local/bin/amphora-agent --config-file /etc/octavia/amphora-agent.conf
    root       408   186  0 Apr15 ?        00:17:30 /usr/bin/python /usr/local/bin/amphora-agent --config-file /etc/octavia/amphora-agent.conf
    root       410   186 14 Apr15 ?        16:23:56 /usr/bin/python /usr/local/bin/amphora-agent --config-file /etc/octavia/amphora-agent.conf

## plug vip
创建loadbalancer时，会向虚拟机发送类似如下的API请求：

    POST /0.5/plug/vip/10.0.0.6

其中10.0.0.6是loadbalancer的vip，amphora-agent会根据请求，更新虚拟机的网卡文件，并添加路由。

    $ cat /etc/network/interfaces.d/eth0.cfg 
    # Generated by Octavia agent
    auto eth0 eth0:0
    iface eth0 inet dhcp
    $ cat /etc/network/interfaces.d/eth1.cfg 
    # Generated by Octavia agent
    auto eth1 eth1:0
    iface eth1 inet dhcp
    iface eth1:0 inet static
    address 10.0.0.6
    broadcast 10.0.0.255
    netmask 255.255.255.0
    $ sudo ip route list
    default via 192.168.0.1 dev eth0 
    10.0.0.0/24 dev eth1  proto kernel  scope link  src 10.0.0.7 
    169.254.169.254 via 10.0.0.1 dev eth1 
    192.168.0.0/24 dev eth0  proto kernel  scope link  src 192.168.0.24

## get interface
API请求：

    GET /0.5/interface/10.0.0.7

根据IP地址找在虚拟机内找到该地址对应的网卡名称，比如eth1

## upload vrrp
API请求：

    PUT /0.5/vrrp/upload

配置keepalived及其启动脚本。在我的环境中，一个master角色的amphorae，keepalived相关配置如下：

    $ cat /var/lib/octavia/vrrp/octavia-keepalived.conf
    vrrp_script check_script {
      script /var/lib/octavia/vrrp/check_script.sh
      interval 5
      fall 2
      rise 2
    }
    vrrp_instance 9d62b4bba15f43f0a3d10be629d502e4 {
     state MASTER
     interface eth1
     virtual_router_id 1
     priority 100
     nopreempt
     garp_master_refresh 5
     garp_master_refresh_repeat 2
     advert_int 1
     authentication {
      auth_type PASS
      auth_pass 51290ec
     }
     unicast_src_ip 10.0.0.7
     unicast_peer {
           10.0.0.8
     }
     virtual_ipaddress {
      10.0.0.6
     }
     track_script {
        check_script
     }
    }
    $ cat /var/lib/octavia/vrrp/check_script.sh         
    #!/bin/sh
    status=0
    for file in /var/lib/octavia/vrrp/check_scripts/*
    do
      echo "Running check script: " $file
      sh $file
      status=$(( $status + $? ))
    done
    exit $status
    $ ll /var/lib/octavia/vrrp/check_scripts/
    total 12
    drwxr-xr-x 2 root root 4096 Apr 15 02:44 ./
    drwxr-xr-x 3 root root 4096 Apr 15 02:44 ../
    -rw-r--r-- 1 root root   86 Apr 15 02:51 haproxy_check_script.sh   

## listeners操作

    PUT /0.5/listeners/d5716c03-e269-4d13-9d03-8ddc21a0dcc4/816339a8-2e17-4b61-8825-c6856d7b7f2e/haproxy
    GET /0.5/listeners/816339a8-2e17-4b61-8825-c6856d7b7f2e
    PUT /0.5/listeners/816339a8-2e17-4b61-8825-c6856d7b7f2e/start

其中，d5716c03是amphorae id，816339a8是listener id。上面三个API请求分别是：

- 更新haproxy配置文件（每个listener对应一个haproxy配置），配置文件的位置是：/var/lib/octavia/816339a8-2e17-4b61-8825-c6856d7b7f2e/haproxy.cfg。同时，新建/etc/init/haproxy-816339a8-2e17-4b61-8825-c6856d7b7f2e.conf服务启动文件（如果不存在）。
- 获取listener状态，主要是看pid文件在不在、proc目录下有没有它的进程、以及haproxy配置文件中有没有配置frontend。
- 启动listener代表的haproxy服务。在使用keepalived场景下，需要重新配置对haproxy的检测脚本。检测脚本中的`haproxy-vrrp-check`命令也是octavia提供的python文件。

listener相关的配置文件：

    $ cat /var/lib/octavia/816339a8-2e17-4b61-8825-c6856d7b7f2e/haproxy.cfg
    # Configuration for lb1
    global
        daemon
        user nobody
        group nogroup
        log /dev/log local0
        log /dev/log local1 notice
        stats socket /var/lib/octavia/816339a8-2e17-4b61-8825-c6856d7b7f2e.sock mode 0666 level user
    defaults
        log global
        retries 3
        option redispatch
        timeout connect 5000
        timeout client 50000
        timeout server 50000
    peers 816339a82e174b618825c6856d7b7f2e_peers
        peer 2mTmbcymLVU8UF0_AOgz9Oa3JUg 10.0.0.8:1025
        peer s1NuOFCDbW2EcfNDIgxiQeauixY 10.0.0.7:1025
    frontend 816339a8-2e17-4b61-8825-c6856d7b7f2e
        option tcplog
        bind 10.0.0.6:80
        mode http
        default_backend 41736521-ee57-4bb0-a049-51b581cb7e62
    backend 41736521-ee57-4bb0-a049-51b581cb7e62
        mode http
        balance roundrobin
        option forwardfor
        server 1746eca3-5e22-41b4-a5e1-736c42b1973f 10.0.0.4:80 weight 1
        server 7270447b-965f-4fa0-8cb4-f350f621ff32 10.0.0.5:80 weight 1

    $ cat /etc/init/haproxy-816339a8-2e17-4b61-8825-c6856d7b7f2e.conf
    description "Properly handle haproxy"

    start on startup

    env PID_PATH=/var/lib/octavia/816339a8-2e17-4b61-8825-c6856d7b7f2e/816339a8-2e17-4b61-8825-c6856d7b7f2e.pid
    env BIN_PATH=/usr/sbin/haproxy
    env CONF_PATH=/var/lib/octavia/816339a8-2e17-4b61-8825-c6856d7b7f2e/haproxy.cfg
    env PEER_NAME=s1NuOFCDbW2EcfNDIgxiQeauixY

    respawn
    respawn limit 2 2

    pre-start script
        [ -r $CONF_PATH ]
    end script

    script
    exec /bin/bash <<EOF
    echo \$(date) Starting HAProxy
    # The -L trick fixes the HAProxy limitation to have long peer names
    $BIN_PATH -f $CONF_PATH -L $PEER_NAME -D -p $PID_PATH

    trap "$BIN_PATH -f $CONF_PATH -L $PEER_NAME -p $PID_PATH -sf \\\$(cat $PID_PATH)" SIGHUP
    trap "kill -TERM \\\$(cat $PID_PATH) && rm $PID_PATH;echo \\\$(date) Exiting HAProxy; exit 0" SIGTERM SIGINT

    while true; do # Iterate to keep job running.

    # Check if HAProxy has failed and re-spawn
    kill -0 \$(cat $PID_PATH)
    if [ \$? -ne 0 ]; then
      echo \$(date) HAProxy failed. Respawning
      exit 1
    fi

    sleep 1 # Don't sleep to long as signals will not be handled during sleep.
    done
    EOF
    end script
    $ cat /var/lib/octavia/vrrp/check_scripts/haproxy_check_script.sh
    haproxy-vrrp-check /var/lib/octavia/816339a8-2e17-4b61-8825-c6856d7b7f2e.sock; exit $?

## plug network

    POST /0.5/plug/network

新建网卡文件，重启新的网卡。
