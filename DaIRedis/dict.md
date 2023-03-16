* 数据结构：
```  
------------
| dictht   |  
------------     -----------------  
| table    | --->| dictEntry*[1] |   
------------     -----------------      ------------- next -------------  
| sizemask |     |      0        | ---> | dictEntry |--->  | dictEntry |--->NULL  
------------                          -------------      -------------  
| used     |                            | k1  | v1  |      | k0  | v0  |  

           ___ dictht (ht[0])
          |
dict.ht ---
          |___ dictht (ht[1])
```
* 哈希算法：
1. 调用流程：`hash = dict->type->hasFunction(key)`,`index = hash & dict->ht[x]->sizemask` (sizemask = dict->ht[x].size-1)
2. 使用的算法是MurmutHash2
* 解决键冲突：采取开链策略
* rehash
1. 为字典的ht[1]分配空间：成倍扩展或者对半收缩
2. 将ht[0]当中的键值对重新计算后转移到ht[1]上
3. 当2步结束后，调换ht[1]和ht[0]
4. 采取**渐进式**rehash：在字典中维持一个变量rehashidx，当`rehashidx == 0`时表示rehash正式开始；在rehash期间，每次对字典执行添加、删除或者查找操作时除了执行指定的操作以外还会顺带将ht[0]在rehashidx索引上的键值对rehash到ht[1]，当rehash工作完成后，rehashidx增1；rehash结束后，rehashidx变为-1