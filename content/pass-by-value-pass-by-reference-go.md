---
title: "Pass by value and Pass by reference in Go"
date: 2018-09-10T01:04:17+05:30
categories: ["go", "golang", "programming", "structs", "pointer"]
author: "Vivek Singh"
---

In Go, everything is passed by value. When we pass arguments to a function, a function always gets the copy of the data passed. For example, passing an int value to function makes a copy of the int, passing a pointer value makes a copy of the pointer but not the data it points to.

When we pass a value to function it is called pass by value and when we pass a pointer to a function it is called pass by reference.

In this blog post, we will see when and how to pass by value or pass by reference.

## Pass by value
By default, everything in Go is passed by value. The function receives the copy of data passed and any changes in the called function will not be reflected in caller function. If you want to make your data immutable (changes that function made to it will not be visible by calling code)

<script src="https://gist.github.com/viveksyngh/ed0dda46cd63847b448b84c44f9443e8.js"></script>

If you run the above code, you will get the following output.

```
"a" value before calling "add1" : 10
"a" value after calling "add1" : 10
Person's age before birthday : 30
Person's age after birthday : 30
```

As we can see in the above example, when we pass by value changes applied in the called function is not visible to calling code. This means called function is getting a copy of the data.

## Pass by reference
To pass anything by reference in Go, we make use of `&` and `*` operator. `&` is address of operator which gives memory address of a variable when applied in front of it.

```go
var a := &i
```

`*`is an operator which is used in conjunction with `& ` operator and returns the value stored at the pointer. It goes in front of a variable which holds a memory address and gets the value pointer was pointing to.

```go
var b := *a
```

<script src="https://gist.github.com/viveksyngh/659011dbd48e1f787ad394fccf05fe15.js"></script>

If you run the above code, you will get the following output.

```
"a" value before calling "add1" : 10
"a" value after calling "add1" : 11
Person's age before birthday : 30
Person's age after birthday : 31
```

As we can see in the example when we pass the reference changes applied in a called function will be visible in calling code.

## Conclusion

As we write Go code, we can ask ourself whether to pass by value or pass by reference.

As we have already seen, passing values is a great way to make our data immutable. Sometimes this is all you need but not more often. If you are not sure whether you want to get updated value in the calling code or not, use pass by reference.

In the case of a struct, if we pass by value it needs to create the copy of the struct every time. Consider the cost involved in creating the copy of a complex structure. If you have a complex structure it is recommended to pass by reference.




