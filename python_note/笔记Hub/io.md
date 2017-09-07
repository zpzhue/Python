## IO

### 打开和关闭文件

`help(open)`获取python内部的帮助

`f = open('./hello.py')`使用open打开文件，返回值是一个file-like对象

`f.read()`读取文件

`f.close()`close方法关闭文件

### 文件对象的操作

* 读
* 写

文件的操作和文件打开方式强相关

#### 文件打开方式和文件操作的相关性

Character Meaning
* 'r'       open for reading (default)
* 'w'       open for writing, **truncating the file first**
* 'x'       create a new file and open it for writing
* 'a'       open for writing, appending to the end of the file if it exists
* 'b'       binary mode
* 't'       text mode (default)
* '+'       open a disk file for updating (reading and writing)
* 'U'       universal newline mode (deprecated)

#### 以mode=r打开文件
```python
f = open('./hello.py', mode='r')
f.write('test') # mode=r 不可写
f.read() # mode=r 可读
f = open('./not_exist.txt', mode='r') # mode=r 文件不存在时， 会抛出FileNotFoundError
%cat hello.py # 使用bash的命令查看hello.py文件，jupyter需运行在Linux中
```
#### 以mode=w打开文件
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
#### 以mode=x打开文件
```python
f = open('./hello.py', mode='x') # mode=x 当文件存在， 会抛出异常 FileExistsError
%rm ./not_exist.txt # 使用bash删除not_exist.txt文件
f = open('./not_exist.txt', mode='x') # mode=x 总是创建新文件
f.read() # mode=x 不可读
f.write('abcd') # mode=x 可写
f.close()
%cat not_exist.txt # 查看文件内容
```
#### 以mode=a打卡文件
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
#### 控制读写的模式(总结)

* r  只读 文件必须存在
* w  只写，先清空文件， 文件不存会创建文件
* x  只写， 文件必须不存在
* a  只写，追加到文件末尾, 文件不存在会创建

* 从读写的方面来看， 只有r可读不可写， 其他都是可写不可读
* 从文件不存在来看，只有r抛出异常，其他都创建新文件
* 从文件存在来看， 只有x抛出异常
* 从是否影响原始内容来看，只有w会清空文件

#### import os
`help(os.open)`系统的open函数，比较底层

#### 以mode=t打开文件
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
#### 以mode=b打开文件
```python
f = open('hello.py', mode='wb')
f.write('马哥教育') # mode=b write参数需为bytes
f.write('马哥教育'.encode()) # 按字节写入， 写入12个字节， 每个中文字符占三个字节
f.close()
```
#### 以mode=r+打开文件
```python
f = open('hello.py', mode='r+') #mode=r+ 可读可写
f.read()
f.write('haha')
f.close()
%cat hello.py
```
#### 以mode=w+打开文件
```python
f = open('hello.py', mode='w+') # mode=w+ 可读可写， 清空文件
f.read()
f.write('haha')
f.close()
%cat hello.py
```
![](images/open3.png)
#### 以mode=r+打开文件的问题


#### mode=+总结










aaa
