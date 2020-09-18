## 类型转换

### int32 和 int 是否是一个类型？如何互转？

不是同一个类型

互转示例

```go
var inter32 int32
var inter int

inter32 = 32
inter = 32

inter = int(inter32)
inter32 = int32(inter)
```

## golang 中 string 和 []byte 类型如何互转

string 不能直接和 byte 数组（比如 [5]byte) 转换，但是 string 可以和 []byte 转换

示例代码

```go
var str string = "test"
var data []byte = []byte(str)
str = string(data)

var dataArr [5]byte = "arr"
str = string(dataArr[:]) //需要先转换成 slice
```

### string 和 []byte 在具体实现有什么区别？为什么要区分它们？性能上有什么差异？（实现差异，性能分析）