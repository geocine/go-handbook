# Channel types

When declaring variables of channel type, the most common instances are like this \(`T` is any valid type\):

```text
var v chan T
```

But you may also see examples as follows :

```text
var v <-chan T
```

Or:

```text
var v chan<- T
```

What the hell are the differences among these `3` definitions? The distinctions are here:

\(1\) `chan T`: The channel can receive and send `T` type data;  
\(2\) `<-chan T`: The channel is **read-only**, which means you can **only receive** `T` type data from this channel;  
\(2\) `chan<- T`: The channel is **write-only**, which means you can **only send** `T` type data to this channel.

The mnemonics is correlating them with channel operations:

```text
v := <-ch  // Receive from ch, and assign value to v.
ch <- v    // Send v to channel ch.
```

`<-chan T` is similar to `v := <-ch`, so it is a receive-only channel, and it is the same as `chan<- T` and `ch <- v`.

Restricting a channel type \(read-only or write-only\) can let compiler do strict checks for you. For example:

```text
package main

func f() (<-chan int) {
    ch := make(chan int)
    return ch
}

func main()  {
    r := f()
    r <- 1
}
```

The compilation generates following errors:

```text
invalid operation: r <- 1 (send to receive-only type <-chan int)
```

Furthermore, the `<-` operator associates with the **leftmost** `chan` possible, i.e., `chan<- chan int` and `chan (<-chan int)` aren't equal: the previous is same as `chan<- (chan int)`, which defines a **write-only** channel whose data type is a channel who can receive and send `int` data; while `chan (<-chan int)` defines a **write-and-read** channel whose data type is a channel who can only receive `int` data.

References:  
[Channel types](https://nanxiao.gitbooks.io/golang-101-hacks/content/posts/unbuffered-and-buffered-channels.html);  
[How to understand "&lt;-chan" in declaration?](https://groups.google.com/forum/#!topic/golang-nuts/ul_K7S3EtOk).

