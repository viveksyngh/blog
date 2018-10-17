---
title: "Getting started with Golang interfaces"
date: 2018-10-17T23:55:32+05:30
categories: ["go", "golang", "interface", "oops"]
author: "Vivek Singh"
draft: true
---

Interfaces in Go provides a way to specify the behavior of an object. Interfaces allow us to write more flexible and scalable code in Go. It is also a mechanism to achieve polymorphism in Go.

```go
type Interface interface {
     Method1(int, string) (int, error)
     Method2(int) bool
     Method3(string)
}
```
Interfaces specify methods (or behavior) but do not provide an implementation for those methods. Types implementing the interface provide an implementation of methods.

## Composition of Interfaces

Interfaces can embed methods from another interface. It embeds all exported (name starting with capital case) and non-exported (name starts with small case) methods from other interfaces.

```go
type Interface1 interface {
    Method1(int, string) (int, error)
    method2(int) bool
    method3(string)
}

type Interface2 interface {
    Method4(int, string) (int, error)
    Method5(string)
    Interface1         //Embedding Interface1 
}
```

Here, `Interface2` will have `Method1`, `method2` and `method3` along with `Method4` and `Method5`.
If an interface Y embed another interface Z, if Y is embedded by X then all methods of Z is also embedded into X.

```go
type I1 interface {
   Method1(int, string) (int, error)
   I2
}

type I2 interface {
   Method2(int, string) (int, error)
   I3
}

type I3 interface {
    Method3(int, string) (int, error)
}
```

In the above example, interface `I1` will have three methods `Method1`, `Method2` and `Method3`.

Circular embedding of interfaces gets detected by the Go compiler and an error will be raised. Interface methods should also have unique names otherwise Go compiler will raise an error.

## Implementing Interfaces
Types implementing those interfaces need to provide an implementation for all methods (or behaviors) in the interface and should define methods with the desired names and signatures (Input and Output parameters).

In Go, interfaces are implemented implicitly. We don’t need to specify that some types implement an interface. Go compiler automatically does that if the type defines all methods of an interface.

```go
type I interface {
   M1(string) string
   M2(string)
}

type T struct {}

func (T) M1(str string) string {
    return str
}

func (T) M2(str string) {
    fmt.Println(str)
}
```

A type can implement multiple interfaces.

```go
type Interface1 interface {
    Method1(string) string
    Method2(string)
}

type Interface2 interface {
    Method3(string) string
}

type T struct {}

func (T) Method1(str string) string {
    return str
}

func (T) Method2(str string) {
    fmt.Println(str)
}

func (T) Method3(str string) string {
    
    return str
}
```

An interface can be implemented by multiple types.

```go
type Interface interface {
    Method1(string) string
}

type T1 struct{}
type T2 struct{}

func (T1) Method1(str string) string {
    return str
}

func (T2) Method1(str string) string {
    return str
}
```

If a Type implements an interface then that Type value can be used wherever we need to use interface value. What it means is, we can pass a value of Type implementing an interface if a function accepts a parameter of the type interface.

```go
type Interface interface {
    Method(string) string
}

type T struct{}

func (T) Method(str string) string {
    return str
}

func f1(i Interface) {
    fmt.Println(i.Method("Hello"))
}

func main() {
    f1(T{})
}
```

An interface type can hold a value of any Type implementing the interface. When we call a method on the interface value then Go dynamically determines which Type method should be invoked based on the type of value it is holding.

```go
type Interface interface {
    Method(str string)
}
type T1 struct{}
type T2 struct{}

func (T1) Method(str string) {
    fmt.Println(str + " from T1 Method")
}

func (T2) Method(str string) {
    fmt.Println(str + " from T2 Method")
}

func main() {
    var i Interface
    i = T1{}
    i.Method("Hello") //invoke Method of T1
    i = T2{}
    i.Method("Hello") //invoke method of T2
}
```

When we invoke `Method` on `i` it checks the type of value it’s holding and calls the corresponding implementation of the `Method`. Above code gives below output when we run it.

```shell
Hello from T1 Method
Hello from T2 Method
```

An interface type value allows accessing only methods of interfaces. It hides methods of the exact type which value it holds.

```go
type Interface interface {
    Method(str string)
}

type T1 struct{}

func (T1) Method(str string) {
   fmt.Println(str + " from T1 Method")
}

func (T1) Method1(str string) {
   fmt.Println(str + " from Method1 of T1")
}

func main() {
    var i Interface
    i = T1{}
    i.Method1("Hello") //throws an error
}
```

when we will try to run above code Go compiler will throw an error saying

```shell
i.Method1 undefined (type Interface has no field or method Method1)
```

## Empty Interface
We can also have an interface which does not have any methods. We call empty interface. Empty interfaces are satisfiable by any type.

```go
type Interface interface {} //An empty interface

type T struct{}

func (T) Method(str string) {
    fmt.Println(" " + " from Method")
}

func main() {
    var i Interface = T{}
    _ = i
}
```

Thanks for the read. If you find something wrong or would like to suggest something please leave a comment below.
