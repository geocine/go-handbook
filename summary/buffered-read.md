# Buffered read

[bufio](https://golang.org/pkg/bufio/) package provides buffered read functions. Let's see an example:

\(1\) Create a `test.txt` file first:

```text
# cat test.txt
abcd
efg
hijk
lmn
```

You can see `test.txt` contains `4` lines.

\(2\) See the following program:

```text
package main

import (
        "bufio"
        "fmt"
        "io"
        "log"
        "os"
)

func main() {
        f, err := os.Open("test.txt")
        if err != nil {
                log.Fatal(err)
        }

        r := bufio.NewReader(f)
        for {
                if s, err := r.ReadSlice('\n'); err == nil || err == io.EOF {
                        fmt.Printf("%s", s)
                        if err == io.EOF {
                                break
                        }
                } else {
                        log.Fatal(err)
                }

        }
}
```

\(a\)

```text
f, err := os.Open("test.txt")
```

Open `test.txt` file.

\(b\)

```text
r := bufio.NewReader(f)
```

`bufio.NewReader(f)` creates a [bufio.Reader](https://golang.org/pkg/bufio/#Reader) struct which implements buffered read function.

\(c\)

```text
for {
    if s, err := r.ReadSlice('\n'); err == nil || err == io.EOF {
        fmt.Printf("%s", s)
        if err == io.EOF {
            break
        }
    } else {
        log.Fatal(err)
    }

}
```

Read and print each line.

The running result is here:

```text
abcd
efg
hijk
lmn
```

We can also use [bufio.Scanner](https://golang.org/pkg/bufio/#Scanner) to implement above "print each line" function:

```text
package main

import (
        "bufio"
        "fmt"
        "log"
        "os"
)

func main() {
        f, err := os.Open("test.txt")
        if err != nil {
                log.Fatal(err)
        }

        s := bufio.NewScanner(f)

        for s.Scan() {
                fmt.Println(s.Text())
        }
}
```

\(a\)

```text
s := bufio.NewScanner(f)
```

`bufio.NewScanner(f)` creates a new [bufio.Scanner](https://golang.org/pkg/bufio/#Scanner) struct which splits the content by line by default.

\(b\)

```text
for s.Scan() {
    fmt.Println(s.Text())
}
```

`s.Scan()` advances the `bufio.Scanner` to the next token \(in this case, it is one optional carriage return followed by one mandatory newline\), and we can use `s.Text()` function to get the content.

We can also customize [SplitFunc](https://golang.org/pkg/bufio/#SplitFunc) function which doesn't separate content by line. Check the following code:

```text
package main

import (
        "bufio"
        "fmt"
        "log"
        "os"
)

func main() {
        f, err := os.Open("test.txt")
        if err != nil {
                log.Fatal(err)
        }

        s := bufio.NewScanner(f)
        split := func(data []byte, atEOF bool) (advance int, token []byte, err error) {
                for i := 0; i < len(data); i++ {
                        if data[i] == 'h' {
                                return i + 1, data[:i], nil
                        }
                }

                return 0, data, bufio.ErrFinalToken
        }
        s.Split(split)
        for s.Scan() {
                fmt.Println(s.Text())
        }
}
```

The `split` function separates the content by "`h`", and the running result is:

```text
abcd
efg

ijk
lmn
```

