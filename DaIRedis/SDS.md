# simple dynamic string
> Redis没有直接用c语言的传统字符串表示，而是自己构建了一种名为简单动态字符串的抽象类型  

* SDS的作用：  
1. 需要一个可以被修改的字符串的值
2. 被用作缓冲区
* SDS的数据结构：  
```c
struct {
    int len; // 记录buf数组中已使用的字节的数量
    int free; // 记录buf数组中未使用的字节的数量
    char buf[]; // 字节数组，用于保存字符串
}
```
* SDS遵循C字符串以空字符结尾的惯例，这样的好处是可以直接使用c中的字符串的函数：如使用`printf`打印`s->buf`

* SDS相较于C字符串的优异之处：  
1. SDS获取字符串的时间复杂度是O(1)
2. 防止缓冲区溢出，在监测到当前字符串剩余空间不足时不会像c语言那样假设目标字符串有着足够的空间，而是会分配一定的空间
3. **二进制安全**：c语言的字符串只可以用来存储文本数据，但是redis的sds为了存储更多类型的数据（视频、音频、图片和压缩文件等）使用了二进制安全的特性--空字符可以在字符串还未结尾时出现

* SDS的内存分配策略：
1. 空间预分配：如果len小于1MB则分配完空间后free与len相同，如果len大于1MB则分配1MB的额外空间
2. 惰性空间释放：将字符串缩短后多出来的空间不进行立即释放，为之后字符串的增长操作预留空间，但是单独提供可以释放空间的api