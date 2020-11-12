---
title: leetcode -- 前缀树
date: 2020-09-23 17:59:25
tags: algorithm
---

leetcode上的一道设计题，要求实现trie这种数据结构，包含插入、查找、前缀查找的操作
[https://leetcode-cn.com/problems/implement-trie-prefix-tree/](https://leetcode-cn.com/problems/implement-trie-prefix-tree/)

首先定义Trie struct

```go
type Trie struct {
    children []*Trie
    isWord bool //是否为单词 
    part byte
}
```

<!-- more -->

children 保存的是当前节点的所有子节点

isWord 如果当前节点为单词结束，标记为true

part 记录当前节点的字符，为byte类型

#### Insert 插入操作

遍历要插入的单词word，判断每一个字符是否出现在children中，如果出现了，继续往下遍历，没有出现，则新建一个节点，并且放入当前节点的children中

结束条件则为遍历的层数跟给定的单词长度一致，并且标记当前节点的isWord属性为true，代表这是一个单词

#### Search 查询操作

同样是遍历所有的children，如果包含当前字符，则继续向下遍历，不包含则结束遍历，返回找不到，当遍历到单词长度的深度的时候，需要判断当前节点的isWord属性是否为true，只有为true的时候才是一个单词，否则返回找不到

#### StartsWith 判断以给定的prefix开头

同search操作相同，只是遍历到最后不需要判断当前节点的isWord属性

完整代码如下：

```go
type Trie struct {
    children []*Trie
    isWord bool //是否为单词 
    part byte
}


/** Initialize your data structure here. */
func Constructor() Trie {
    return Trie{}
}


/** Inserts a word into the trie. */
func (this *Trie) Insert(word string)  {
    this.insert(word, 0)
}

func (this *Trie) insert(word string, height int) {
    if len(word) == height {
        this.isWord = true
        return
    }
    // 取一个字母查找
    w := word[height]
    child := this.matchChild(w)
    // 不存在新建一个节点加入
    if child == nil {
        child = &Trie{part: w}
        this.children = append(this.children, child)
    }
    // 继续遍历下一级
    child.insert(word, height + 1)
}

func (this *Trie) matchChild(w byte) *Trie {
    for _, child := range this.children {
        if child.part == w {
            return child
        }
    }
    return nil
}


/** Returns if the word is in the trie. */
func (this *Trie) Search(word string) bool {
    return this.search(word, 0)
}

func (this *Trie) search(word string, height int) bool {
    if len(word) == height {
        if this.isWord {
            return true
        }
        return false
    }
    for _, child := range this.children {
        if child.part == word[height] {
            return child.search(word, height + 1)
        }
    }
    return false
}


/** Returns if there is any word in the trie that starts with the given prefix. */
func (this *Trie) StartsWith(prefix string) bool {
    return this.searchStartsWith(prefix, 0)
}

func (this *Trie) searchStartsWith(word string, height int) bool {
    if len(word) == height {
        return true
    }
    for _, child := range this.children {
        if child.part == word[height] {
            return child.searchStartsWith(word, height + 1)
        }
    }
    return false
}


/**
 * Your Trie object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Insert(word);
 * param_2 := obj.Search(word);
 * param_3 := obj.StartsWith(prefix);
 */
```

