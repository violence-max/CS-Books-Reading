> 压缩列表（ziplist）是列表键和哈希键的底层实现之一  
* 数据结构：  
| zlbytes | zltail | zllen | entry1 | entry2 | ... | entryN | zlend |  
1. zlbytes : 计算整个压缩列表占用的内存字节数  
2. zltail : 记录压缩列表表头节点至表尾节点的有多少个字节  
3. zllen : **记录压缩列表所包含的节点数量**
4. entryX : 节点  
    1. previous_entry_length : **前一个节点的长度**
    2. encoding : **content属性所包含的数据类型和长度（编解码方式）**
    3. content : 内容
5. zlend : 特殊值0xFF，用于标记压缩列表的末端
* **连锁更新**：
插入或者删除节点时可能因为新插入的节点的长度或者被删除的节点的前一个节点的长度问题会引起ziplist的多次内存重新分配的操作