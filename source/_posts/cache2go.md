---
title: cache2go 源码解读
date: 2020-6-28 14:31:19
tags: go
---

引用官方的说明

>   Concurrency-safe golang caching library with expiration capabilities.
>
>   是一个并行安全的golang缓存库，带有过期功能

go的开源缓存库，对go新手来说比较简单，看了一遍源码，对其中的源码做下解释记录，方便日后重温

代码中为了并行安全，在读写操作中都加了锁（除去一些不变的值，例如：CacheItem的key、data、createdOn）

<!-- more -->

项目目录结构：

```bash
├── LICENSE.txt
├── README.md
├── benchmark_test.go
├── cache.go
├── cache_test.go
├── cacheitem.go
├── cachetable.go
├── errors.go
└── examples
    ├── callbacks
    │   └── callbacks.go
    ├── dataloader
    │   └── dataloader.go
    └── mycachedapp
        └── mycachedapp.go
```

其中主要就三个文件cache.go、cacheitem.go、cachetable.go，其它是一些测试以及example，下面分别对这三个文件做下解释

#### cache.go

```go
var (
    cache = make(map[string]*CacheTable)
    mutex sync.RWMutex
)

func Cache(table string) *CacheTable {
    mutex.RLock()
    t, ok := cache[table]
    mutex.RUnlock()

    if !ok {
      mutex.Lock()
      t, ok = cache[table]
      // Double check whether the table exists or not.
      if !ok {
        t = &CacheTable{
          name:  table,
          items: make(map[interface{}]*CacheItem),
        }
        cache[table] = t
      }
      mutex.Unlock()
    }

    return t
}
```

cache.go文件比较简单，首先是两个全局变量 ，cache和mutex，cache是个map，其中放的是一个一个的CacheTable对象的指针，Cache方法首先检查cache中是否有指定的table，没有的话创建一个，然后放到cache中，并且返回新加的这个CacheTable对象

#### cacheitem.go

```go
type CacheItem struct {
    sync.RWMutex

    // The item's key.
    key interface{}
    // The item's data.
    data interface{}
    // 过期时间
    lifeSpan time.Duration

    // 创建时间
    createdOn time.Time
    // 上一次被访问时间
    accessedOn time.Time
    // 访问次数计数
    accessCount int64

    // item过期的回调函数，可以有多个
    aboutToExpire []func(key interface{})
}
```

包含以下方法

```go
// 初始化CacehItem
func NewCacheItem(key interface{}, lifeSpan time.Duration, data interface{}) *CacheItem {}
// keepalive item
func (item *CacheItem) KeepAlive() {}
// 返回item的lifeSpan accessedOn createdOn accessCount key data ...这些方法
func (item *CacheItem) LifeSpan() time.Duration {}
// 设置过期的回调函数
func (item *CacheItem) SetAboutToExpireCallback(f func(interface{})) {}
// 添加过期回调函数
func (item *CacheItem) AddAboutToExpireCallback(f func(interface{})) {}
```

cacheitem比较简单，看下注释就可以

#### cachetable.go

CacheTable是管理CacheItem的

```go
type CacheTable struct {
    sync.RWMutex

    // The table's name.
    name string
    // 所有的 cached items 都放在items属性中
    items map[interface{}]*CacheItem

    // 定时器，到时见就会自动检查（遍历所有的CacheItem）
    cleanupTimer *time.Timer
    // Current timer duration.
    cleanupInterval time.Duration

    // 日志
    logger *log.Logger

    // 以下是一些操作的回调函数
    // Callback method triggered when trying to load a non-existing key.
    loadData func(key interface{}, args ...interface{}) *CacheItem
    // Callback method triggered when adding a new item to the cache.
    addedItem []func(item *CacheItem)
    // Callback method triggered before deleting an item from the cache.
    aboutToDeleteItem []func(item *CacheItem)
}
```

包含的一些方法看源码中的注释就可以，比较好理解，重点关注下几个方法

其中有个Add方法，添加一个item，回调用addInternal似有方法

```go
func (table *CacheTable) addInternal(item *CacheItem) {
    // Careful: do not run this method unless the table-mutex is locked!
    // It will unlock it for the caller before running the callbacks and checks
    table.log("Adding item with key", item.key, "and lifespan of", item.lifeSpan, "to table", table.name)
    table.items[item.key] = item

    // Cache values so we don't keep blocking the mutex.
    expDur := table.cleanupInterval
    addedItem := table.addedItem
    table.Unlock()

    // 检查是否设置了添加item的回调，如果有则执行
    if addedItem != nil {
      for _, callback := range addedItem {
        callback(item)
      }
    }

    // If we haven't set up any expiration check timer or found a more imminent item.
	  // 如果当前cachetable的cleanupInterval为0或者新加入的item的lifeSpan小于cleanupInterval则触发过期检查函数
    if item.lifeSpan > 0 && (expDur == 0 || item.lifeSpan < expDur) {
      table.expirationCheck()
    }
}
```

在初始化cachetable的时候没有设置定时器，在加入第一个item的时候启用定时器，并且在每加入一个item都会触发过期检查去更新定时器

过期检查是遍历一遍所有数据，找出最先过期的数据，把这个时间当成定时器下次检查的时间（合理设置定时器）

```go
func (table *CacheTable) expirationCheck() {
    table.Lock()
    if table.cleanupTimer != nil {
      	table.cleanupTimer.Stop()
    }
    if table.cleanupInterval > 0 {
        table.log("Expiration check triggered after", table.cleanupInterval, "for table", table.name)
    } else {
      	table.log("Expiration check installed for table", table.name)
    }

    // To be more accurate with timers, we would need to update 'now' on every
    // loop iteration. Not sure it's really efficient though.
    now := time.Now()
    smallestDuration := 0 * time.Second
  	// 遍历table中的所有items，找到最先过期的那一个item
    for key, item := range table.items {
        // Cache values so we don't keep blocking the mutex.
        item.RLock()
        lifeSpan := item.lifeSpan
        accessedOn := item.accessedOn
        item.RUnlock()
				
      	// lifeSpan 永不过期
        if lifeSpan == 0 {
          	continue
        }
        if now.Sub(accessedOn) >= lifeSpan { // 已经过期删除
            // Item has excessed its lifespan.
            table.deleteInternal(key)
        } else {
            // Find the item chronologically closest to its end-of-lifespan.
          	// smallestDuration == 0 第一个缓存数据添加进来的时候，把第一个缓存数据的lifeSpan时间做为下次检查时间间隔
            if smallestDuration == 0 || lifeSpan-now.Sub(accessedOn) < smallestDuration {
              smallestDuration = lifeSpan - now.Sub(accessedOn)
            }
        }
    }

    // Setup the interval for the next cleanup run.
    table.cleanupInterval = smallestDuration
    if smallestDuration > 0 {
      	// 启动一个goruntine定时器，在smallestDuration之后重新掉用expirationCheck方法检查
        table.cleanupTimer = time.AfterFunc(smallestDuration, func() {
          	go table.expirationCheck()
        })
    }
    table.Unlock()
}
```

Value方法获取CacheTable中指定的key对应的数据，有则返回，没有执行loadData回调

```go
func (table *CacheTable) Value(key interface{}, args ...interface{}) (*CacheItem, error) {
    table.RLock()
    r, ok := table.items[key]
    loadData := table.loadData
    table.RUnlock()

    if ok {
        // Update access counter and timestamp.
        r.KeepAlive()
        return r, nil
    }

    // Item doesn't exist in cache. Try and fetch it with a data-loader.
    if loadData != nil {
        item := loadData(key, args...)
        if item != nil {
            table.Add(key, item.lifeSpan, item.data)
            return item, nil
        }

        return nil, ErrKeyNotFoundOrLoadable
    }

    return nil, ErrKeyNotFound
}
```

在代码的最后有个MostAccessed方法，该方法返回的是最多被访问的前n个缓存数据，首先定义一个CacheItemPair结构体，该结构体存放每个item的key以及访问次数

```go
type CacheItemPair struct {
    Key         interface{}
    AccessCount int64
}
```

再声明了一个切片类型的CacheItemPairList实现了sort接口的三个方法，可以掉用sort.Sort(p)方法

```go
type CacheItemPairList []CacheItemPair

func (p CacheItemPairList) Swap(i, j int)      { p[i], p[j] = p[j], p[i] }
func (p CacheItemPairList) Len() int           { return len(p) }
// 找的是最多被访问的缓存数据，所以 p[i].AccessCount > p[j].AccessCount
func (p CacheItemPairList) Less(i, j int) bool { return p[i].AccessCount > p[j].AccessCount }
```

排序完成后，遍历排序后的数据返回前n个

```go
var r []*CacheItem
c := int64(0)
for _, v := range p {
   if c >= count {
      break
   }

   item, ok := table.items[v.Key]
   if ok {
      r = append(r, item)
   }
   c++
}
```


