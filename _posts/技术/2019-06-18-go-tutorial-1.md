---
layout: post
title: Go 语言学习圣经学习笔记-入门1
category: 技术
---
## 1.入门

### 1.1 hello world

```bash
go run helloworld.go 运行go文件
go build helloworld.go 编译go文件
./helloworld 编译后可以直接运行
```

Go语言通过包组织，类似其他语言的库/模块。以package语句开始。
main包比较特殊，定义了一个独立可执行的程序，而不是一个库。main里的main函数，是整个程序执行的入口。

Go 不需要在语句或者生命的末尾添加分号，除非一行有多条语句。编译器会主动把特定符号的换行符转换为分号，因此换行符的位置，会影响解析。
例如函数的左括号必须和函数声明在一行，且位于末尾，不能独占一行。表达式应该在符号后换行，不能在符号前。

Go 在代码格式上采取很强硬的态度。应该养成始终使用gofmt格式化自己代码的习惯。

goimports可以根据代码需要自动的添加和删除import声明，但这个工具没有包含在标准的分发包中，需要用以下的命令进行安装：
> go get golang.org/x/tools/cmd/goimports

### 1.2 命令行参数

os.Args获取命令行的参数，os.Args[0]是命令本身的名字。其他元素是启动时传给它的参数。
导入多个包时，可以写成列表形式。
注释以//开头。
变量会在声明时被初始化为其类型的零值。

:=是短变量声明。定义一个或者多个变量并根据他们的初始值为这些变量赋予适当的类型的语句。
自增是语句，所以j = i++非法，而且只能放在变量后，--i也是非法的。

Go只有for循环一种循环语句。循环的三个部分不需要括号包围。initialization是可选的，但是如果存在必须是一条简单语句，即：短变量声明、 自增语句、 赋值语句或函数调用。condition是布尔表达式，如果是true时执行。post在循环执行结束后执行。然后循环。
这三个部分都可以省略。如果省略前两部分，分号也可以省略。

>for initialization; condition; post {
// zero or more statements
}

```go
// Echo1 prints its command-line arguments.
package main

import (
    "fmt"
    "os"
)

func main() {
    var s, sep string
    sep = " "
    for i := 1; i < len(os.Args); i++ {
        s += sep + os.Args[i]
    }
    fmt.Println(s)
}

```

for循环的另一种形式，是在某种数据类型的区间上遍历，如字符串或切片。
Go语言不允许使用无用的局部变量，所以解决的办法是使用空标识符_,空标识符用在任何语法需要变量名但是程序逻辑不需要的时候。

```go
// Echo2 prints its command-line arguments.
package main

import (
	"fmt"
	"os"
)

func main() {
	s, sep := "", ""
	for _, arg := range os.Args[1:] {
		s += sep + arg
		sep = " "
	}
	fmt.Println(s)
}

```

声明变量有好几种方式，下面这些都等价：
>s := ""
var s string
var s = ""
var s string = ""

第一种只能用在函数内部，不能用于包变量。实践中一般使用前两种的某个，初始值重要就显示指定变量的类型，否则就是用隐式初始化。
String连接代价高昂，推荐的方法是使用strings.Join函数来进行连接。

## 1.3 查找重复的行

```go
// Dup1 prints the text of each line that appears more than
// once in the standard input, preceded by its count.
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	counts := make(map[string]int)
	input := bufio.NewScanner(os.Stdin)
	for input.Scan() {
		counts[input.Text()]++
	}
	// NOTE: ignoring potential errors from input.Err()
	for line, n := range counts {
		if n > 1 {
			fmt.Printf("%d\t%s\n", n, line)
		}
	}
}
```
map存储键值的集合，对于集合元素，提供常数时间的存取或测试操作。键可以是任意类型，主要可以使用==进行比较。值可以为任意类型。make创建空map.
map的迭代顺序并不确定，从实践来看，该顺序随机，每次都会变化。

bufio包，主要用来处理输入和输出。Scanner类型是该包最有用的特性之一，它读取输入并将其拆成行或单次，通常是处理行的输入最简单的方法。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main()  {
	counts := make(map[string]int)
	files := os.Args[1:]
	if len(files) == 0{
		countLines(os.Stdin, counts)
	}else{
		for _, arg := range files{
			f, err := os.Open(arg)
			if err != nil{
				fmt.Fprint(os.Stderr,"dup2: %v\n",err)
				continue
			}
			countLines(f,counts)
			f.Close()
		}
	}
	for line,n := range counts{
		if n > 0{
			fmt.Printf("%d\t%s\n",n,line)
		}
	}
}
func countLines(f *os.File, counts map[string]int)  {

	input := bufio.NewScanner(f)
	for input.Scan(){
		counts[input.Text()]++
	}
}

```

nui等于其他语言的NULL。os.Open返回两个值，第一个值是打开的文件，第二个值是error类型的值，如果为空则文件成功打开。
函数和包级别的变量可以任意顺序声明，并不影响调用。
map函数传递实际上是引用传递。

io/ioutil包的ReadFile函数，一次将文件全部读取到内存中，然后使用strings.Split函数进行字符串分割。

实现上Scanner、ReadFile、WriteFile都是用*os.File的Read和Write方法，但是大多数程序员很少需要直接调用低级函数，高级函数使用要更容易。
