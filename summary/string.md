# String

In `Go`, string is an immutable array of bytes. So if created, we can't change its value. E.g.:

```text
package main

func main()  {
    s := "Hello"
    s[0] = 'h'
}
```

The compiler will complain:

```text
cannot assign to s[0] 
```

To modify the content of a string, you could convert it to a `byte` array. But in fact, you **do not** operate on the original string, just a copy:

```text
package main

import "fmt"

func main()  {
    s := "Hello"
    b := []byte(s)
    b[0] = 'h'
    fmt.Printf("%s\n", b)
} 
```

The result is like this:

```text
hello
```

Since `Go` uses `UTF-8` encoding, you must remember the `len` function will return the string's byte number, not character number:

```text
package main

import "fmt"

func main()  {
    s := "日志log"
    fmt.Println(len(s))
} 
```

The result is:

```text
9
```

Because each Chinese character occupied `3` bytes, `s` in the above example contains `5` characters and `9` bytes.

If you want to access every character, `for ... range` loop can give a help:

```text
package main
import "fmt"

func main() {
    s := "日志log"
    for index, runeValue := range s {
        fmt.Printf("%#U starts at byte position %d\n", runeValue, index)
    }
}
```

The result is:

```text
U+65E5 '日' starts at byte position 0
U+5FD7 '志' starts at byte position 3
U+006C 'l' starts at byte position 6
U+006F 'o' starts at byte position 7
U+0067 'g' starts at byte position 8
```

Reference:  
[Strings, bytes, runes and characters in Go](https://blog.golang.org/strings);  
[The Go Programming Language](http://www.gopl.io/).

