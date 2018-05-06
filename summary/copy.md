# copy

The definition of built-in `copy` function is [here](https://golang.org/pkg/builtin/#copy):

> func copy\(dst, src \[\]Type\) int
>
> The copy built-in function copies elements from a source slice into a destination slice. \(As a special case, it also will copy bytes from a string to a slice of bytes.\) The source and destination may overlap. Copy returns the number of elements copied, which will be the minimum of len\(src\) and len\(dst\).

Let's see a basic example in which source and destination slices aren't overlapped:

```text
package main

import (
    "fmt"
)

func main() {
    d := make([]int, 3, 5)
    s := []int{2, 2}
    fmt.Println("Before copying (destination slice): ", d)
    fmt.Println("Copy length is: ", copy(d, s))
    fmt.Println("After copying (destination slice): ", d)

    d = make([]int, 3, 5)
    s = []int{2, 2, 2}
    fmt.Println("Before copying (destination slice): ", d)
    fmt.Println("Copy length is: ", copy(d, s))
    fmt.Println("After copying (destination slice): ", d)

    d = make([]int, 3, 5)
    s = []int{2, 2, 2, 2}
    fmt.Println("Before copying (destination slice): ", d)
    fmt.Println("Copy length is: ", copy(d, s))
    fmt.Println("After copying (destination slice): ", d)

}
```

In the above example, the destination slice's length is `3`, and the source slice's length can be `2`, `3`, `4`. Check the result:

```text
Before copying (destination slice):  [0 0 0]
Copy length is:  2
After copying (destination slice):  [2 2 0]
Before copying (destination slice):  [0 0 0]
Copy length is:  3
After copying (destination slice):  [2 2 2]
Before copying (destination slice):  [0 0 0]
Copy length is:  3
After copying (destination slice):  [2 2 2]
```

We can make sure the number of copied elements is indeed the minimum length of source and destination slices.

Let's check the overlapped case:

```text
package main

import (
    "fmt"
)

func main() {
    d := []int{1, 2, 3}
    s := d[1:]

    fmt.Println("Before copying: ", "source is: ", s, "destination is: ", d)
    fmt.Println(copy(d, s))
    fmt.Println("After copying: ", "source is: ", s, "destination is: ", d)

    s = []int{1, 2, 3}
    d = s[1:]

    fmt.Println("Before copying: ", "source is: ", s, "destination is: ", d)
    fmt.Println(copy(d, s))
    fmt.Println("After copying: ", "source is: ", s, "destination is: ", d)
}
```

The result is like this:

```text
Before copying:  source is:  [2 3] destination is:  [1 2 3]
2
After copying:  source is:  [3 3] destination is:  [2 3 3]
Before copying:  source is:  [1 2 3] destination is:  [2 3]
2
After copying:  source is:  [1 1 2] destination is:  [1 2]
```

Through the output, we can see no matter the source slice is ahead of destination or not, the result is always as expected. You can think the implementation is like this: the data from source slice are copied to a temporary place first, then the elements are copied from temporary to destination slice.

`copy` requires the source and destination slices are the same type, and an exception is the source is string while the destination is `[]byte`:

```text
package main

import (
    "fmt"
)

func main() {
    d := make([]byte, 20, 30)
    fmt.Println(copy(d, "Hello, 中国"))
    fmt.Println(string(d))
} 
```

The output is:

```text
13
Hello, 中国
```

Reference:  
[copy\(\) behavior when overlapping](https://groups.google.com/forum/#!msg/Golang-Nuts/HI6RI18S8L0/v6xevVPeS9EJ).

