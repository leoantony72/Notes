---
tags:
  - go
  - strings
---

#### Multi Line String
---
```go
Str := `Multi line
String`
```


# Strings Functions
---
==Package: strings==

```go
import strings
```


#### Contains(string, string)
---
```go
fmt.Println(strings.Contains("Hello World", "World"))
// Output: true
```
#### Count(string, string)
---
```go
fmt.Println(strings.Count("Hello World", "l"))
// Output: 3
```
#### Index(string, string)
---
```go
fmt.Println(strings.Index("leo", "e"))
// Output: 1
```

#### Repeat("a", 5)
---
```go
fmt.Println(strings.Repeat("l", 5))
// Output: lllll
```

#### Join([]string, string)
---
```go
fmt.Println(strings.Join([]string{"hello","world"}, "-"))
// Output: hello-world
```

#### Replace(string, string, string, int)
---
**func strings.Replace(s string, old string, new string, n int) string**

Replace returns a copy of the string s with the first n non-overlapping instances of old replaced by new. If old is empty, it matches at the beginning of the string and after each UTF-8 sequence, yielding up to k+1 replacements for a k-rune string. If n < 0, there is no limit on the number of replacements.
```go
fmt.Println(strings.Replace("foo","o","p",-1))
// Output: "fpp"
```

#### Split(string,string)
---
```go
fmt.Println(strings.Split("a-b-c", "-"))
// Output: [a b c]
```

