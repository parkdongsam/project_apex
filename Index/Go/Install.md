# Install
---
### 1. Download   
[Go Archive](https://golang.org/dl/)
### 2. extract it into /usr/local  
```bash
tar -C /usr/local -xzf go1.14.2.linux-amd64.tar.gz
```
### 3. Add PATH environment variable  
File : /etc/profile or $HOME/.profile
```bash
export PATH=$PATH:/usr/local/go/bin
```
### 4. Test
```bash
$ vi test.go
package main
import "fmt"
func main() {
	fmt.Printf("hello, world\n")
}
```

```bash
$ go build hello.go
```

```bash
$ ./hello
hello, world
```

### 5. Useful Link
https://golang.org/doc/
