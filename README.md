# EffectiveGo
My learning note of effective Go

## Formatting
1. Use gofmt (or `go fmt`)
2. Use tab
3. No line length limit. Use extra tab to wrap lines

## Code comments
1. C style
2. Each package should have a package comment. You can write it in any file in that package
3. Each exported name should have a go doc

## Package naming
1. Package name is lower-case, single word. No _, -, CapitalCase, or camelCase.
2. Package name doesn't need to be unique.
3. Package name == directory name
4. Don't repeat package name in functions. Example:
    1. bufio.Reader is good. bufio.bufReader is not.
    2. ring.New is enough. ring.NewRing or ring.Ring is repetitive.

## Getter and setter
1. Getter doesn't have "get" in function name
2. Setter does have "set" in function name

Example:
```go
owner := obj.Owner()
if owner != user {
    obj.SetOwner(user)
}
```

## Interface naming
Avoid `Read`, `Write`, `Close`, `Flush`, `String` unless you want to override them.

Example of overriding `String`
```go
func (b bin) String() string {
    return fmt.Sprintf("%b", b)
}
```

## MixedCaps
Use CaptitalCase and camelCase. No _.

# Semicolon and bracket
1. Semicolon is not needed. Compiler will add it for you.
2. No new bracket in new line
```go
if i < f()  // wrong!
{           // wrong!
    g()
}
```

# Control flow
1. There is no `while` for `do while`.
2. If and switch accept an initialization statement.
```go
if err := file.Chmod(0664); err != nil {
    log.Print(err)
    return err
}
```
3. You don't need `while`. `for` can do it.
```go
// Like a C for
for init; condition; post { }

// Like a C while
for condition { }

// Like a C for(;;)
for { }
```
4. Smart use of for
```go
// Loop array/map
for key, value := range oldMap {
    newMap[key] = value
}

// Index only
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}

// Value only
sum := 0
for _, value := range array {
    sum += value
}

// Parallel assignment
// Reverse a
for i, j := 0, len(a)-1; i < j; i, j = i+1, j-1 {
    a[i], a[j] = a[j], a[i]
}
```
5. Switch is more general. It can replace most if-else-if-else chain.
```go
func unhex(c byte) byte {
    switch {
    case '0' <= c && c <= '9':
        return c - '0'
    case 'a' <= c && c <= 'f':
        return c - 'a' + 10
    case 'A' <= c && c <= 'F':
        return c - 'A' + 10
    }
    return 0
}
```
6. No need to break after each case. It doesn't automatically fall down to next.
7. Switch cases can be grouped.
```go
func shouldEscape(c byte) bool {
    switch c {
    case ' ', '?', '&', '=', '#', '+', '%':
        return true
    }
    return false
}
```
8. You can break/continue outer loop by labelling it.
```go
Loop:
	for n := 0; n < len(src); n += size {
		switch {
		case src[n] < sizeOne:
			if validateOnly {
				break   // break switch
			}
			size = 1
			update(src[n])

		case src[n] < sizeTwo:
			if n+1 >= len(src) {
				err = errShortInput
				break Loop  // break outer loop
			}
			if validateOnly {
				break   // break switch
			}
			size = 2
			update(src[n] + src[n+1]<<shift)
		}
	}
```
9. Use switch to check object type. (Just like `instanceof` in Java)
```go
var t interface{}
t = functionOfSomeType()
switch t := t.(type) {
default:
    fmt.Printf("unexpected type %T\n", t)     // %T prints whatever type t has
case bool:
    fmt.Printf("boolean %t\n", t)             // t has type bool
case int:
    fmt.Printf("integer %d\n", t)             // t has type int
case *bool:
    fmt.Printf("pointer to boolean %t\n", *t) // t has type *bool
case *int:
    fmt.Printf("pointer to integer %d\n", *t) // t has type *int
}
```