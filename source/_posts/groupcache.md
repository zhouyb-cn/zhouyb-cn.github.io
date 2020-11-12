---
title: groupcache 源码系列（一）一致性hash算法
date: 2020-7-10 14:35:01
tags: go
---

#### Hash

```go
// Hash 在此代码中代表函数类型，返回的是无符号32位整数
type Hash func(data []byte) uint32
```
<!-- more -->

#### Map

```go
type Map struct {
    hash     Hash // 上面的Hash类型
    replicas int  // 副本数量，指定一个key 会生成 replicas 个副本，保证hash环的均匀分布
    keys     []int // 排序后的key切片
    hashMap  map[int]string // hashmap 保存每一个hash对应的key
}
```

#### New方法

```go
// 返回一个Map类型的对象
func New(replicas int, fn Hash) *Map {
    m := &Map{
      replicas: replicas,
      hash:     fn,
      hashMap:  make(map[int]string),
    }
    if m.hash == nil {
      // 不指定Hash方法的时候 默认使用crc32.ChecksumIEEE方法进行hash
      m.hash = crc32.ChecksumIEEE
    }
    return m
}
```

#### Add方法

```go
func (m *Map) Add(keys ...string) {
    // 遍历添加的所有key
    for _, key := range keys {
      	// 对每一个key复制制定的replicas个数 0key 1key 2key ... 对这些key进行hash算法，得到对应的hash值，这些值对应hash环上的一个点，把这些hash值放入keys切片中保存，并且把hash值对应的key保存到hashMap中
        for i := 0; i < m.replicas; i++ {
            hash := int(m.hash([]byte(strconv.Itoa(i) + key)))
            m.keys = append(m.keys, hash)
            m.hashMap[hash] = key
        }
    }
  	// 对keys排序
    sort.Ints(m.keys)
}
```

#### Get方法

```go
func (m *Map) Get(key string) string {
	if m.IsEmpty() {
		return ""
	}
	
    // 对要查找的key进行hash算法
	hash := int(m.hash([]byte(key)))

	// Binary search for appropriate replica.
    // 查找hash后的key在hash环中的位置
	idx := sort.Search(len(m.keys), func(i int) bool { return m.keys[i] >= hash })

	// Means we have cycled back to the first replica.
	if idx == len(m.keys) {
		idx = 0
	}
	//返回hashMap中对应的index值 m.keys[idx] 是hash值 hashMap[m.keys[idx]] 为hash值对应的key
	return m.hashMap[m.keys[idx]]
}
```



