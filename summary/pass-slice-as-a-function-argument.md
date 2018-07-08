# Pass slice as a function argument

In `Go`, the function parameters are passed by value. With respect to use slice as a function argument, that means the function will get the copies of the slice: a pointer which points to the starting address of the underlying array, accompanied by the length and capacity of the slice. Oh boy! Since you know the address of the memory which is used to store the data, you can tweak the slice now. Let's see the following example:

```text
package main

import (
    "fmt"
)

func modifyValue(s []int)  {
    s[1] = 3
    fmt.Printf("In modifyValue: s is %v\n", s)
}
func main() {
    s := []int{1, 2}
    fmt.Printf("In main, before modifyValue: s is %v\n", s)
    modifyValue(s)
    fmt.Printf("In main, after modifyValue: s is %v\n", s)
}
```

The result is here:

```text
In main, before modifyValue: s is [1 2]
In modifyValue: s is [1 3]
In main, after modifyValue: s is [1 3]
```

You can see, after running `modifyValue` function, the content of slice `s` is changed. Although the `modifyValue` function just gets a copy of the memory address of slice's underlying array, it is enough!

See another example:

```text
package main

import (
    "fmt"
)

func addValue(c []int) {
    c = append(c, 3)
    fmt.Printf("In addValue: c is %v\n", c)
}

func main() {
    s := []int{1, 2}
    fmt.Printf("In main, before addValue: s is %v\n", s)
    addValue(s)
    fmt.Printf("In main, after addValue: s is %v\n", s)
}
```

The result is like this:

```text
In main, before addValue: s is [1 2]
In addValue: c is [1 2 3]
In main, after addValue: s is [1 2]
```

This time, the `addValue` function doesn't take effect on the `s` slice in `main` function. That's because it just manipulates its local copy `c` of `s`.

When we use `append` to update `c` in this example, it just so happens that the capacity of the underlying array pointed to by `s` is exceeded. When this happens, `append` allocates a new array, copies the values from the old array new, and updates pointer in `c` to point to the new, bigger array.
Since `c` is a copy of `s`, our changes to `c` are not reflected in `s`. The pointer to the underlying array in `s` is _not_ updated, and therefore still points to the old array.

If the underlying array happens to have enough capacity to accommodate the appended data, `append` does not have to allocate a new array. In this case `append` appends data to the array that `s` points to. Below is an example of this:

```text
package main
import (
    "fmt"
)

func addValue(c []int) {
    c = append(c, 3)
    fmt.Printf("In addValue: c is %v\n", c)
}

func main() {
    s := make([]int, 2, 3)
    s[0] = 1
    s[1] = 2
    fmt.Printf("In main, before addValue: s is %v, len: %+v, cap: %+v\n", s, len(s), cap(s))
    addValue(s)
    fmt.Printf("In main, after addValue: s is %v, len: %+v, cap: %+v\n", s, len(s), cap(s))

    s = s[:cap(s)]
    fmt.Printf("In main, after addValue: s[:cap(s)] is %v, len: %+v, cap: %+v\n", s, len(s), cap(s))
}
```

The result is like this:

```text
In main, before addValue: s is [1 2], len: 2, cap: 3
In addValue: c is [1 2 3]
In main, after addValue: s is [1 2], len: 2, cap: 3
In main, after addValue: s[:cap(s)] is [1 2 3], len: 3, cap: 3
```

Since we cannot know how much data was appended by the `addValue` function, it is seldom the case that you want to use this effect.
If you really want a function to change the content of a slice, you can instead pass the address of the slice:

```text
package main

import (
    "fmt"
)

func addValue(c *[]int) {
    *c = append(*c, 3)
    fmt.Printf("In addValue: c is %v\n", c)
}

func main() {
    s := []int{1, 2}
    fmt.Printf("In main, before addValue: s is %v\n", s)
    addValue(&s)
    fmt.Printf("In main, after addValue: s is %v\n", s)
}     
```

The result is like this:

```text
In main, before addValue: s is [1 2]
In addValue: c is &[1 2 3]
In main, after addValue: s is [1 2 3]
```

