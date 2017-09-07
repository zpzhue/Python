# 打开和关闭文件

`help(open)`获取python内部的帮助

`f = open('./hello.py')`使用open打开文件，返回值是一个file-like对象

`f.read()`读取文件

`f.close()`close方法关闭文件

# 文件对象的操作

* 读
* 写

文件的操作和文件打开方式强相关

## 文件打开方式和文件操作的相关性

Character Meaning
* 'r'       open for reading (default)
* 'w'       open for writing, **truncating the file first**
* 'x'       create a new file and open it for writing
* 'a'       open for writing, appending to the end of the file if it exists
* 'b'       binary mode
* 't'       text mode (default)
* '+'       open a disk file for updating (reading and writing)
* 'U'       universal newline mode (deprecated)

## 以mode=r打开文件

```python
f = open('./hello.py', mode='r')
f.write('test') # mode=r 不可写
f.read() # mode=r 可读
f = open('./not_exist.txt', mode='r') # mode=r 文件不存在时， 会抛出FileNotFoundError
%cat hello.py # 使用bash的命令查看hello.py文件，jupyter需运行在Linux中
```

## 以mode=w打开文件

```python
f = open('./hello.py', mode='w')
f.read() # mode=w  不可读
f.write('abcd') # mode=w 可写
f.close()
%cat hello.py # mode=w 会清空原文件
f = open('./hello.py', mode='w') # mode=w 即使打开后，不做任何操作，也会清空文件
f.close()
f = open('./not_exist.txt', mode='w') # mode=w 当文件不存在时，会创建新文件
f.close()
%ls ./not_exist.txt
```

重点是以mode=w打开文件之后，首先清除源文件内容

## 以mode=x打开文件

```python
f = open('./hello.py', mode='x') # mode=x 当文件存在， 会抛出异常 FileExistsError
%rm ./not_exist.txt # 使用bash删除not_exist.txt文件
f = open('./not_exist.txt', mode='x') # mode=x 总是创建新文件
f.read() # mode=x 不可读
f.write('abcd') # mode=x 可写
f.close()
%cat not_exist.txt # 查看文件内容
```

## 以mode=a打卡文件

```python
f = open('./hello.py', mode='a')
f.read() # mode=a 不可读
f.write('abcd') # mode=a 可写
f.close()
%cat hello.py # mode=a 写入的内容追加到文档末尾
%rm not_exist.txt # 删除not_exist.txt文件
f = open('./not_exist.txt', mode='a') # 以mode=a打开一个不存在的文件，会创建文件
f.write('abcd')
f.close()
%cat not_exist.txt
```

## 控制读写的模式(总结)

* r  只读 文件必须存在
* w  只写，先清空文件， 文件不存会创建文件
* x  只写， 文件必须不存在
* a  只写，追加到文件末尾, 文件不存在会创建

* 从读写的方面来看， 只有r可读不可写， 其他都是可写不可读
* 从文件不存在来看，只有r抛出异常，其他都创建新文件
* 从文件存在来看， 只有x抛出异常
* 从是否影响原始内容来看，只有w会清空文件

## import os

`help(os.open)`系统的open函数，比较底层

## 以mode=t打开文件

```python
f = open('./hello.py', mode='rt')
s = f.read() # mode=t 读入的内容是字符串
type(s)
f.close()
```

![](images/open1.png)

```python
f = open('./hello.py', mode='rb')
s = f.read() # mode=b 读入的是bytes
s
type(s)
f.close()
```

![](images/open2.png)

## 以mode=b打开文件

```python
f = open('hello.py', mode='wb')
f.write('马哥教育') # mode=b write参数需为bytes
f.write('马哥教育'.encode()) # 按字节写入， 写入12个字节， 每个中文字符占三个字节
f.close()
```

## 以mode=r+打开文件

```python
f = open('hello.py', mode='r+') #mode=r+ 可读可写
f.read()
f.write('haha')
f.close()
%cat hello.py
```

![](images/open7.png)

## 以mode=w+打开文件

```python
f = open('hello.py', mode='w+') # mode=w+ 可读可写， 清空文件
f.read()
f.write('haha')
f.close()
%cat hello.py
```
![](images/open6.png)

## 以mode=r+打开文件 ***导致的问题***

```python
%cat hello.py
f = open('hello.py', mode='r+') #mode=r+ 可读可写
f.write('he')
f.read()
f.close()
%cat hello.py
```

![](images/r+1.png)

## mode=+总结

当打开文件的时候， 解释器会持有一个指针， 指向文件的某个位置

当我们读写文件的时候，总是从指针处开始向后操作，并且移动指针

当mode=r时， 指针是指向0(文件开始)

mode=a时， 指针指向EOF(文件末尾)

## 以mode=a+打开文件的问题

```python
f = open('hello.py', mode='a+')
f.read()
f.write('heihei')
f.close()
%cat hello.py
```

![](images/a+1.png)

## 以mode=+打开文件 ***导致的问题***

```python
f = open('hello.py', mode='+') # 单独的+不能工作， mode里必须有且仅有rwxa中的一个
```

![](images/+1.png)

## mode=+总结
当mode包含+时， 会增加额外的读写操作， 也就说原来是只读的，会增加可写的操作， 原来是只写的，会增加可读的操作，但是+不改变其他行为

## 指针的位置

```python
f = open('hello.py') # 打开文件默认使用rt模式打开
f.tell() # 获取当当前文件指针的位置
f.read()
f.tell()
f.close()
```

![](images/指针1.png)

### seek指针的使用
`help(f.seek)`获取跳转指针的位置的帮助

seek(cookie, whence=0, /) method of io.TextIOWrapper instance
    Change stream position.

* 0 -- start of stream (the default); offset should be zero or positive
* 1 -- current stream position; offset may be negative
* 2 -- end of stream; offset is usually negative



    按字节移动文件指针
    当whence为start(0)(默认值)， offset可以是任意整数
    当whence为current(1)或者end(2)， offset只能为0

```python
f.seek(2) # 指针跳到文件第3个字符
f.seek(2,0) # 和上面的命令的含义一样
f.seek(0, 0) # 把指针指向文件第一个字符
f.seek(4, 1) # 不支持
f.seek(4, 2) # 不支持
f.seek(0, 1) # 支持，指针位置不变
f.seek(0, 2) # 支持，跳转尾部
f.close()
```

### mode=b
    按字节移动文件指针
    当whence为start(0)(默认值)， offset可以是任意整数
    当whence为current(1)或者end(2)， offset也可以是任意整数

```python
f = open('hello.py', mode='rb') # rb
f.seek(3) # 按字节操作
f.read().decode()
f.seek(3)
f.seek(3, 1) # whence=current(1) offset可以是任意整数
f.read().decode()
f.seek(3, 2)
f.seek(-3, 2) # whence=end(2) offset可以是任意整数
f.seek(13) # 向后超出范围允许
f.seek(-13, 2) # 向前超出范围不允许
f.close()
```

### 超出范围后写内容

当seek超出文件末尾， 不会有异常， tell也会超出文件末尾， 但是写数据的时候，还是会从文件末尾开始写

```python
f = open('hello.py', mode='a+')
f.seek(0, 1) # 如截图，共十二个字符
f.seek(13) # 指针跳转超出
f.write('abc') # 在指针超出的情况下写文件
f.read()
f.tell() # tell 总是以字节来计算
f.close()
```

![](images/seek1.png)

## 指针总结

* 文件指针按字节操作
* tell方法返回当前文件指针位置
* seek方法移动文件指针
* whence 参数 SEEK_SET(0) 从0开始向后移动offset个字节, SEEK_CUR(1) 从当前位置向后移动offset个字节, SEEK_END(2) 从EOF向后移动offset个字节
* offset是整数
* 当mode为t时， whence为SEEK_CUR或者SEEK_END时， offset只能为0
* 文件指针不能为负数
* 读文件的时候从文件指针(pos)开始向后读
* 写文件的时候从min(EOF,pos)处开始向后写
* 以append模式打开的时候，无论文件指针在何处，都从EOF开始写

## 缓冲区

### mode=b模式的时候

```python
f = open('hello.py', 'wb')
f.write(b'abc')
f.flush() # flush方法刷新缓冲区
%cat hello.py
f.write(b'abc')
%cat hello.py
f.close() # close 也会刷新缓冲区
%cat hello.py
```

![](images/buffer1.png)

```python
f = open('hello.py', 'wb', buffering=5) # 缓冲区大小设置为5个字节
f.write(b'abc')
%cat hello.py # 文件为空
f.write(b'abc') # 检查缓冲区是否足够写入当前字节，如果不够，flush缓冲区，然后再把当前字节写入缓冲区
%cat hello.py
f.close()
```

![](images/buffer2.png)

### mode=t模式的时候

buffering=1代表以line buffer模式运行，每遇到换行符flush一次

```python
import io
io.DEFAULT_BUFFER_SIZE 缓存区大小
f = open('hello.py', 'wt', buffering=1) # 文本模式， buffering 为1 使用line buffer
f.write('abc')
%cat hello.py
f.write('\n') # text mode 当写入换行符时，会刷新缓冲区
%cat hello.py
f.close()
```

![](images/buffer3.png)

buffering=5，只要buffering大于1，代表开启缓存区而不是指定缓冲区大小

```python
f = open('hello.py', 'wt', buffering=5) # text mode buffering　大于1， 缓冲区大小为io.DEFAULT_BUFFER_SIZE 缓冲区满的时候，flush缓冲区，连同本次写入的内容
f.write('abc')
%cat hello.py
f.write('\n')
%cat hello.py
f.close
```

![](images/buffer4.png)

`f = open('hello.py', 'wt', buffering=0)`文本模式不能关闭缓冲区

## buffer总结

buffering=-1
* 二进制模式： DEFAULT_BUFFER_SIZE
* 文本模式: DEFAULT_BUFFER_SIZE
buffering=0
* 二进制模式: unbuffered
* 文本模式： 不允许
buffering=1
* 二进制模式： 1
* 文本模式： line buffering
buffering>1
* 二进制模式：buffering
* 文本模式： DEFAULT_BUFFER_SIZE


二进制模式： 判断缓冲区剩余位置是否足够存放当前字节，如果不能，先flush， 在把当前字节写入缓冲区，如果当前字节大于缓冲区大小， 直接flush

文本模式： line buffering，遇到换行就flush， 非line buffering，如果当前字节加缓冲区中的字节，超出缓冲区大小，直接flush缓冲区和当前字节

特殊文件对象有特殊的刷新方式

flush和close可以强制刷新缓冲区

## IO的一些其它指令

 ```python
 f = open('hello.py','w')
 f.write('abc\nbcd\ncde')
 f.close()
 %cat hello.py
 ```

![](images/open8.png)

```python
f = open('hello.py')
f.readline() # 按行读
f.readlines() # 每行数据作为一个元素保存到列表中
f.close()
f = open('hello.py', 'r+')
f.readable() # 判断文件是否可读
f.read(2) # 从当前位置开始向后读2个字符
f.read(2)
f.read(-1) # 从当前位置读到最后一个字符
```

![](images/read1.png)

```python
f.seek(0) # 指针挪到第一个字符
f.readline(1) # 读入min(size, EOF-pos)个字符, 如果遇到换行符， 提前返回
f.writelines(['abc\n', 'cbd\n']) # 一次性写入多组文本
```

![](images/writlines1.png)

```python
f.seekable() # 文件指针是否可移动
f.fileno() # python程序打开文件的序列号
f.isatty() # 是否是tty文件，tty在linux中是命令行的名称
sys.stderr.isatty() # 是否是tty文件
f.mode # 显示打开文件的模式
f.name # 文件名
f.close()
```

![](images/command1.png)

```python
f = open('hello.py', mode='r+b')
f.writelines([b'aaaa\n',b'bbbb\n', b'cccc\n', b'dddd\n', b'abc\n', b'abc\n', b'cbd\n']) # 二进制模式的writelines的使用方法
f.flush()
f.seek(0)
f.readline() # 读一行
f.readlines() # 读全部文件，返回一个列表
f.close()
```

![](images/breadline.png)

## 文件数量限制

```python
lst = []
for x in range(2000): # 打开2000个文件
    lst.append(open('haha.txt'))
len(lst)
for x in lst: # 关闭已经打开的文件
    x.close()
```

![](images/limate1.png)

Linux中查看进程文件限制的命令ulimit -a

# 上下文管理




















aaa
