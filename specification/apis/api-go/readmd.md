# KCLVM 的 Go 语言 API

Go 语言 API 是 KCLVM 为外部用户提供的编程接口，通过 API 可以实现 KCLVM 命令行提供的功能，同时 API 可以满足更强的定制性需求。

## API 抽象模型

Go 语言 API 的抽象模型如下图：

```
┌─────────────────┐         ┌─────────────────┐           ┌─────────────────┐
│     kcl files   │         │   KCLVM-Go-API  │           │  KCLResultList  │
│  ┌───────────┐  │         │                 │           │                 │
│  │    1.k    │  │         │                 │           │                 │
│  └───────────┘  │         │                 │           │  ┌───────────┐  │         ┌───────────────┐
│  ┌───────────┐  │         │  ┌───────────┐  │           │  │ KCLResult │──┼────────▶│x.Get("a.b.c") │
│  │    2.k    │  │         │  │ Run(path) │  │           │  └───────────┘  │         └───────────────┘
│  └───────────┘  │────┐    │  └───────────┘  │           │                 │
│  ┌───────────┐  │    │    │                 │           │  ┌───────────┐  │         ┌───────────────┐
│  │    3.k    │  │    │    │                 │           │  │ KCLResult │──┼────────▶│x.Get("k", &v) │
│  └───────────┘  │    │    │                 │           │  └───────────┘  │         └───────────────┘
│  ┌───────────┐  │    ├───▶│  ┌───────────┐  │──────────▶│                 │
│  │setting.yml│  │    │    │  │RunFiles() │  │           │  ┌───────────┐  │         ┌───────────────┐
│  └───────────┘  │    │    │  └───────────┘  │           │  │ KCLResult │──┼────────▶│x.JSONString() │
└─────────────────┘    │    │                 │           │  └───────────┘  │         └───────────────┘
                       │    │                 │           │                 │
┌─────────────────┐    │    │                 │           │  ┌───────────┐  │         ┌───────────────┐
│     Options     │    │    │  ┌───────────┐  │           │  │ KCLResult │──┼────────▶│x.YAMLString() │
│WithOptions      │    │    │  │MustRun()  │  │           │  └───────────┘  │         └───────────────┘
│WithOverrides    │────┘    │  └───────────┘  │           │                 │
│WithWorkDir      │         │                 │           │                 │
│WithDisableNone  │         │                 │           │                 │
└─────────────────┘         └─────────────────┘           └─────────────────┘
```

其中输入的文件包含 KCL 文件和 `setting.yml` 配置文件，`Options` 可以用于指定额外的参数和工作目录等信息。“KCLVM-Go-API”部分是提供的 KCLVM 执行函数，执行函数根据输入文件和额外的参数执行 KCL 程序，最终输出 `KCLResultList` 结果。`KCLResultList` 是一个 `KCLResult` 构成的列表，每个 `KCLResult` 对应一个生成的配置文件或 `map[string]interface{}`。

## 例子

```go
package main

import (
	"fmt"

	"gitlab.alipay-inc.com/ant-cas/KCLVM/apis/kcl-go"
)


func main() {
	const k_code = `
import kcl_plugin.hello

name = "kcl"
age = 1

two = hello.add(1, 1)

schema Person:
    name: str = "kcl"
    age: int = 1

x0 = Person{}
x1 = Person{age:101}
`

	result := kcl.MustRun("hello.k", kcl.WithCode(k_code)).First()
	fmt.Println(result.YAMLString())

	fmt.Println("----")
	fmt.Println("x0.name:", result.Get("x0.name"))
	fmt.Println("x1.age:", result.Get("x1.age"))

	fmt.Println("----")

	var person struct {
		Name string
		Age  int
	}
	fmt.Printf("person: %+v\n", result.Get("x1", &person))
}
```

输入结果:

```yaml
age: 1
name: kcl
two: 2
x0:
    age: 1
    name: kcl
x1:
    age: 101
    name: kcl

----
x0.name: kcl
x1.age: 101
----
person: &{Name:kcl Age:101}
```
