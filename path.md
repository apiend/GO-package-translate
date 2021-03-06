Package path


Overview ▾

Package path implements utility routines for manipulating slash-separated paths.
path包实现实用例程 处理斜杠分隔路径。


Variables
```golang
var ErrBadPattern = errors.New("syntax error in pattern")
```
ErrBadPattern indicates a globbing pattern was malformed.
ErrBadPattern 表示文件名匹配模式是异常的。


func Base
```golang
func Base(path string) string
```
Base returns the last element of path. 
Trailing slashes are removed before extracting the last element. 
If the path is empty, Base returns ".". 
If the path consists entirely of slashes, Base returns "/".
Base返回 path最后的元素.
在提前最后一个元素前,结尾的斜杆会被删除.
如果path是空的, Base 返回".". 
如果path 只有斜杆, Base 返回  "/".


▹ Example
```golang
package main

import (
	"fmt"
	"path"
)

func main() {
	fmt.Println(path.Base("/a/b"))
}
```


func Clean
```golang
func Clean(path string) string
```
Clean returns the shortest path name equivalent to path by purely lexical processing. 
It applies the following rules iteratively until no further processing can be done:
Clean 返回 最短path 等价 于由单纯的词法处理的path
它适用于 以下规则迭代直到没有进一步的处理可以做到：

```golang
1. Replace multiple slashes with a single slash.
2. Eliminate each . path name element (the current directory).
3. Eliminate each inner .. path name element (the parent directory)
   along with the non-.. element that precedes it.
4. Eliminate .. elements that begin a rooted path:
   that is, replace "/.." by "/" at the beginning of a path.
   
1. 用单个斜杆替代多个斜杆
2. 清除每个 .  路径名元素(代表当前目录)
3. 清除每个 .. 路径名元素(代表父目录) 和它前面的非 .. 元素。
4. 清除以root 路径 开始的..  : 也就是说在路径开始的时候用 "/" 替换 "/.."
   
```

The returned path ends in a slash only if it is the root "/".

If the result of this process is an empty string, Clean returns the string ".".

See also Rob Pike, “Lexical File Names in Plan 9 or Getting Dot-Dot Right,” http://plan9.bell-labs.com/sys/doc/lexnames.html

只有是 root "/"时  返回的path最后是 斜杆.
如果该进程的结果是一个空字符串,Clean 返回 字符串 ".".
见Rob Pike,"Plan 9的 词汇文件名 或右键获取",http://plan9.bell-labs.com/sys/doc/lexnames.html


▾ Example
```golang
package main

import (
	"fmt"
	"path"
)

func main() {
	paths := []string{
		"a/c",
		"a//c",
		"a/c/.",
		"a/c/b/..",
		"/../a/c",
		"/../a/b/../././/c",
	}

	for _, p := range paths {
		fmt.Printf("Clean(%q) = %q\n", p, path.Clean(p))
	}

}
```

func Dir
```golang
func Dir(path string) string
```
Dir returns all but the last element of path, typically the path's directory. 
After dropping the final element using Split, the path is Cleaned and trailing slashes are removed. 
If the path is empty, Dir returns ".". If the path consists entirely of slashes followed by non-slash bytes, Dir returns a single slash. 
In any other case, the returned path does not end in a slash.
Dir返回 所有但是path最后的元素, 通常是 path 目录.
适用Splits 留下 最后的元素后,path 是 Cleaned 并且 已除最后的斜杆.
如果path是空的,Dir 返回 " . " .  如果path 完全由斜杆 跟着非斜杆字节, Dir 返回 单个 斜杆.
在其他情况下, 返回path 不以斜杆做末尾.


▹ Example
```golang
package main

import (
	"fmt"
	"path"
)

func main() {
	fmt.Println(path.Dir("/a/b/c"))
}
```


func Ext
```golang
func Ext(path string) string
```
Ext returns the file name extension used by path. 
The extension is the suffix beginning at the final dot in the final slash-separated element of path; 
it is empty if there is no dot.
Ext  适用path返回文件扩展名.
扩展是 以在路径的最后一个斜杠分隔的元素最后的点开始的 后缀 
如果没有点 那它是空的.


▹ Example
```golang
package main

import (
	"fmt"
	"path"
)

func main() {
	fmt.Println(path.Ext("/a/b/c/bar.css"))
}
```


func IsAbs
```golang
func IsAbs(path string) bool
```
IsAbs returns true if the path is absolute.
IsAbs 如果path是绝对路径返回true


▹ Example
```golang
package main

import (
	"fmt"
	"path"
)

func main() {
	fmt.Println(path.IsAbs("/dev/null"))
}
```

func Join
```golang
func Join(elem ...string) string
```
Join joins any number of path elements into a single path, adding a separating slash if necessary. 
The result is Cleaned; in particular, all empty strings are ignored.
Join 加入任何路径元素数 到 单个路径, 如果需要的话加个斜杆分隔.
结果是 Cleaned; 特别是,所有的空字符都忽略.


▹ Example
```golang
package main

import (
	"fmt"
	"path"
)

func main() {
	fmt.Println(path.Join("a", "b", "c"))
}
```


func Match
```golang
func Match(pattern, name string) (matched bool, err error)
```
Match returns true if name matches the shell file name pattern. The pattern syntax is:
Match 如果name 匹配shell文件名 pattern 返回true. pattern 的语法是:
```golang
pattern:
	{ term }
term:
	'*'         matches any sequence of non-/ characters
	'?'         matches any single non-/ character
	'[' [ '^' ] { character-range } ']'
	            character class (must be non-empty)
	c           matches character c (c != '*', '?', '\\', '[')
	'\\' c      matches character c

character-range:
	c           matches character c (c != '\\', '-', ']')
	'\\' c      matches character c
	lo '-' hi   matches character c for lo <= c <= hi
```
Match requires pattern to match all of name, not just a substring. The only possible returned error is ErrBadPattern, when pattern is malformed.
Match 需要 pattern 匹配所有name,而不是一个子串. 当pattern是异常 返回的错误只可能是 ErrBadPattern

func Split
```golang
func Split(path string) (dir, file string)
```
Split splits path immediately following the final slash. 
separating it into a directory and file name component. 
If there is no slash path, Split returns an empty dir and file set to path. 
The returned values have the property that path = dir+file.
Split 以最后的斜杆 分裂 path.
分隔它到目录和file名组件.
如果没有斜杆path ,Split 返回 空dir和把file设置到path


▹ Example
```golang
package main

import (
	"fmt"
	"path"
)

func main() {
	fmt.Println(path.Split("static/myfile.css"))
}
```


