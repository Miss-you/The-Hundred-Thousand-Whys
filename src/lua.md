## lua table的key区分大小写吗

要区分
只要有一个字符不一样就是两个键

## lua 的优势

1）lua是一个免费、小巧、简单、强大、高效、轻量级的嵌入式的脚本语言,lua当前的发行版本5.3.1只有276k。

2）它是用C语言开发的项目，所以可以在大部分的操作系统上运行

3）lua是目前速度最快的脚本语言，既可以提升语言的灵活性还可以最大限度的保留速度

4）其语法非常简单，没有特例

5）lua还可以作为C的API来使用

## lua 的不足

1）lua没有强大的库，所以很多功能实现起来没有python、perl、ruby等脚本语言简洁

2）lua的异常处理功能饱受争议，虽然其提供了pcall和xpcall的异常处理函数

3）lua原生语言中没有提供对unicode编码的支持，虽然可以通过一些折中的办法实现 http://www.cppblog.com/darkdestiny/archive/2009/04/25/81055.html

4）没有提供在C++中应用很广泛的a?b:c的三元运算符操作

5）没有switch...case...语法，只能通过if..elseif..elseif..else..end的方式折中实现

6)在循环时没有提供continue语法

7）没有C++中应用广泛的a++和a+=1等操作

8）lua的索引是从1开始的，而不是我们熟悉的0（string，table）

9）当你给一个元素赋值为nil时相当于这个元素不存在

10）lua的数值类型只有number是没有int，float，double等之分的

11）lua中没有类的概念，其类是通过table的形式来实现的

12）lua中只有nil和false是表示假的，零在lua中是为真的

13）很多程序需要（）标示才能运行，比如a={["b"]=5},print(a.b)是可运行的，但是 {["b"]=5}.b就会报错，需要（{["b"]=5}）.b才可以

## 为什么 Lua 没有全局锁？

因为 Lua 是多协程，本质是并发，最终工作的就一个线程
Python 是多线程，会有并行问题

不需要全局锁

## 软件如何能得到认可

Language are accepted and evolved by a social process, not a technical or technological one. Successful languages must have modest or minimal computer resource request.
