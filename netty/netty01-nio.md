# 1、三大组件
# 2、ByteBuffer
# 3、文件编程
## 3.1 FileChannel
**注意**
> FileChannel只能工作在**阻塞模式**下
**获取**
不能直接打开FileChannel，必须通过FileInputStream、FileOutputStream或者RandomAccessFile来获取FileChannel，它们都有getChannel方法
- 通过FileInputStream获取的channel只能读
- 通过FileOutputStream获取的channel只能写
- 通过RandomAccessFile是否能读写根据构造RandomAccessFile时的读写模式决定

**读取**

会从channel读取数据填充ByteBuffer，返回值表示读到了多少字节，-1表示达到了文件的末尾
```java
int readBytes = channel.read(buffer);
```

**写入**
```java
ByteBuffer buffer = ...;
buffer.put(...); //存入数据
buffer.flip(); //切换模式

while(buffer.hasRemaining()) {
  channel.write(buffer);
}
```
在while中调用channel.write是因为write方法并不能保证一次将buffer中的内容全部写入channel

**关闭**

channel必须关闭，不过调用了FileInputStream、FileOutputStream或者RandomAccessFile的close方法会间接地调用channel的close方法

**位置**

获取当前位置
```java
long pos = channel.position();
```
设置当前位置
```java
long newPos = ...;
channel.position(newPos);
```
设置当前位置时，如果设置为文件的末尾
- 这时读取会返回-1
- 这时写入，会追加内容，但要注意如果position超过了文件末尾，再写入时再新内容和原末尾之间会有空洞(00)

**大小**

使用size方法获取文件的大小

**强制写入**

操作系统处于性能的考虑，会将数据缓存，不是立刻写入磁盘，可以调用force(true)方法将文件内容和元数据（文件的权限等信息）立刻写入磁盘

## 3.2 两个Channel传输数据
