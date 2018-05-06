# range

`for ... range` clause can be used to iterate `5` types of variables: array, slice, string, map and channel, and the following sheet gives a summary of the items of `for ... range` loops:

| Type | 1st item | 2nd item |
| --- | --- | --- | --- | --- | --- |
| Array | index | value |
| Slice | index | value |
| String | index \(rune\) | value \(rune\) |
| Map | key | value |
| Channel | value |  |

For array, slice, string and map, if you don't care about the second item, you can omit it. E.g.:

```text
package main

import "fmt"

func main() {
    m := map[string]struct{} {
        "alpha": struct{}{},
        "beta": struct{}{},
    }
    for k := range m {
        fmt.Println(k)
    }
}
```

The running result is like this:

```text
alpha
beta
```

Likewise, if the program doesn't need the first item, a blank identifier should occupy the position:

```text
package main

import "fmt"

func main() {
    for _, v := range []int{1, 2, 3} {
        fmt.Println(v)
    }
}
```

The output is:

```text
1
2
3
```

For channel type, the `close` operation can cause `for ... range` loop exit. See the following code:

```text
package main

import "fmt"

func main() {
    ch := make(chan int)
    go func(chan int) {
        for _, v := range []int{1, 2} {
            ch <- v
        }
        close(ch)
    }(ch)

    for v := range ch {
        fmt.Println(v)
    }
    fmt.Println("The channel is closed.")
}
```

Check the outcome:

```text
1
2
The channel is closed.
```

We can see `close(ch)` statement in another goroutine make the loop in main goroutine end.

