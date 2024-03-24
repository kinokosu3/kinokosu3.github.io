---
title: Golang中的panic、defer、recover
date: 2022-01-02 14:29:10
image: cover.png
categories: 
  - Golang
---

### panic与defer的组合

在Golang的goroutine中会有一个结构体g，记录了一些关于goroutine的信息，里面有一个defer链表的头指针，还有一个panic链表的头指针，出现一个panic，就往该panic链表中塞入一个，头指针指向当前执行的那个。执行一个简单的panic：

```golang
func main() {
    defer A1()
    panic("main panic")
}
```

main函数注册defer函数A1后，发生panic，返回去执行defer。和正常函数结束后defer不同，panic后执行defer会将defer结构体中的_panic指向当前执行的panic，用来记录这个defer是由这个panic触发的。这样做的好处是防止defer函数执行时再次发生panic，不会重复执行当前defer。

一个关于panic->defer->panic的例子：

```golang
func A1(){
    panic("A1 panic")
}

func main() {
    defer A1()
    panic("main panic")
}
```

发生panic后，首先将defer A1的started和_panic赋值，在A1也发生panic后，当前goroutine的defer链表中只有A1一个defer，而且A1已经开始且有panic指向，这时候会发生什么呢，先来看看panic结构体。panic结构体的信息如下：

```golang
type _panic sturct{
    argp unsafe.Pointer // 在panic期间调用的defer函数
    arg interface{} //panic的参数
    link *_panic //链接之前的panic
    recovered bool //是否被恢复
    aborted bool //是否被终止
}
```
这时候Golang会把之前的main panic的aborted置为true，表示它已经终止，然后输出panic(顺序为发生的时间正序)。

### recover、panic、defer的组合

```golang
func A1(){
    recover()
}

func main() {
    defer A1()
    panic("main panic")
}

```

main panic发生后，执行defer A1，发生recover，main panic的recovered被置为true，表示被恢复。在每个defer执行完之后，Golang都会去检查一遍这个defer的panic是否被恢复，如果恢复，则将其从panic链表中删除。

如果带有recover的defer函数也发生panic呢？

```golang
func A1(){
    recover()
    panic("A1 panic")
}

func A2(){

}

func main() {
    defer A2()
    defer A1()
    panic("main panic")
}

```
这时候panic->defer，发现defer A1已经执行，然后将panic main的aborted置为true，终止，这时候defer A2就是由A1 panic触发的了。这时候输出panic信息时，main panic出正常输出且会带有recovered字样，没有被摘除。