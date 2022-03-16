# 知识来源

- Redis 6.x源代码
- Redis 5 源码设计与分析
- Redis设计与实现
- Redis深度历险 核心原理与应用实践
- Redis开发与运维

# 数据结构

Redis是键值存储服务器，键总是字符串结构，值有5种类型：字符串对象、列表对象、哈希对象、集合对象、有序集合对象。

## 字符串对象

简单动态字符串对象（SDS）是Redis实现的 **二进制安全（C/C++中使用 \0 表示字符串结束，如果字符串本身内部包含这个字符，那么就会产生截断。）** 的字符串结构。SDS在Redis 3.2.x之前的实现是:

```c
struct sds {
    int len;
    int free;
    char buf[];
}
```

> Redis使用 **柔性数组** ，也就是 ``char buf[]`` 只能放在结构体声明的尾部，通过malloc()函数动态分配内存。柔性数组的地址和结构体地址是连续的，使得内存查找更快（直接借助偏移量）。

但是这样的问题还是存在的，Redis通常会存储很多的短字符串，这样 ``int len`` 和 ``int free`` 就占用了很大的空间。Redis使用一个标志字段来区分。

Redis这样的设计不仅节省了内存，并且按照1字节对齐，使得sdshdr5到sdsdhr64都可以通过 ``(char*)sh+hdrlen`` 得到buf的地址。

Redis的SDS具体实现：

```c
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags;
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len;
    uint8_t alloc;
    unsigned char flags;
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len;
    uint16_t alloc;
    unsigned char flags;
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len;
    uint32_t alloc;
    unsigned char flags;
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len;
    uint64_t alloc;
    unsigned char flags;
    char buf[];
};
```

- 创建Redis会将SDS_TYPE_5转换为SDS_TYPE_8类型。
- sdsfree()函数实现内存的直接释放，但是为了避免频繁的内存申请，Redis提供了sdsclear()函数重置len计数器。

## 有序集合对象

- 使用跳跃表来实现。当元素个数少且都是短字符串时。使用压缩列表实现。
  - 时间复杂度：索引的高度 * 每层索引遍历元素的个数。
  - 空间复杂度：O(n)

## 哈希对象

- 使用压缩列表实现。
  - 压缩列表使用字节数组实现。

## 列表对象

- 使用快速链表实现，本质是双向链表+压缩列表。

# 单机数据库

## 服务器

## 客户端

## 持久化

### AOF

### RDB

### 事件

### 发布订阅

### 事务

### 其他功能

# 分布式数据库

## 复制

## 哨兵机制

## 集群