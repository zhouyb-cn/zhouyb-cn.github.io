---
title: Go中var、new、make介绍
date: 2020-10-25 20:59:25
tags: golang
---

go中声明变量有很多方式，五花八门（乱七八糟），实际上写多了会固化几种写法，比如全局变量会在外面用var声明，方法内变量一般都是 x := xxx 这样，至于channel、slice、map都会使用make关键字，而new实际当中用的很少

### var

使用var关键字声明变量，系统会默认为变量分配内存空间，并赋值该类型的0值

```go
var i int 
fmt.Println(i) // 0
```

如果声明指针类型的变量，就不会分配内存空间，默认是nil

```go
var i *int
fmt.Println(i == nil) // true
```

此时需要配合new关键字使用，i指向一块内存空间

```go
i = new(int)
fmt.Println(i) // 0xc000018030
fmt.Println(*i) // 0
```

### new和make的区别

new 和 make 都会分配内存，new返回指向该类型内存地址的指针，并赋值该类型的0值，make只能用于channel、slice、map，返回的是引用类型，并初始化该类型

项目中使用较多的是slice，如果我们能明确知道slice的长度，声明的时候直接指定，能够避免reslice扩容造成的元素搬迁（追求性能，不过也是一个好习惯）

s := make([]int, 0, 10)

如果不能明确知道，一般这么定义

s := make([]int, 0)

今天发现一段代码这么定义直接使用的

var s []int

在实际项目中一直习惯于用make关键字来初始化slice，很少用关键字var，这种定义的slice为nil，比较好奇为啥没有出错，查看发现后面用了append操作，append操作可以接收nil的切片，在内部会使用mallocgc进行内存分配，所以这么写是可以的，觉得容易记混的话，还是都使用make来进行初始化，就会避免出现不必要的bug