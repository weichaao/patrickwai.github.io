---
layout: post
title: "Java中哈希表(Hashtable)是如何实现的"
description: ""
category: java
tags: []
---

内部类Entry
-----------

Hashtable中用一个内部类Entry来呈现每一个单位数据，用来构建哈希表的每一个数据是一个Entry对象。Entry类在JDK中的呈现：

    /**
     * Hashtable bucket collision list entry
     */
    private static class Entry<K,V> implements Map.Entry<K,V> {
        int hash;
        final K key;
        V value;
        Entry<K,V> next;

        protected Entry(int hash, K key, V value, Entry<K,V> next) {

        }
    ... ...
    }

假设我们保存下面一组数据，第一列作为key, 第二列作为value。

    {“one", 1}
    {"two", 2}
    {"three", 3}
    {"four", 4}

写一个演示程序：

    import java.util.Hashtable;

    public class Main {

        public static void main(String[] args) {
            Hashtable<String, Integer> numbers = new Hashtable<String, Integer>();
            numbers.put("one", 1);
            numbers.put("two", 2);
            numbers.put("three", 3);
            numbers.put("four", 4);
            numbers.put("five", 5);

            Integer n = numbers.get("two");
            Integer nn = numbers.get("six");
            
            if(n != null)
                System.out.println(n);
                System.out.println(nn);
        }
    }

Hashtable会计算每个对象关键字的hash，根据hash的不同，每一个Entry对象保存在不同的Bucket里。


Entry在Bucket里是如何表示的？
--------------------------

当我们插入一个新的Entry对象时，即用Hashtable的put(key, value)方法。
在put方法里：

* 计算key的hash值
* 计算index值，作为数组bucket的下标，即bucket[index]
* 保存对象到Bucket

bucket的大小是通过计算得到一个合理的值，所有index值一样的Entry对象，存放在同一个bucket里。
每一个bucket实际上是一个Entry, 每一个Entry有一个方法next(类似于指针)指向下一个Entry对象，构成一个单链表。这里用到链表的数据结构，保证了可以遍历bucket中所有的Entry对象。
既然是一个链接表，根据数据结构知识，自然我们的Entry对象需要有一个指向下一个对象的指针，即Entry对象需要有这些属性：key，value，next。


如何构造hash函数？
------------------
hash值，如何生成？对于每个对象的hash值，要保证每一个hash值都不一样。
在Java SDK中， 字符串计算hash的方法如下：

    // String类的hashCode()方法
    // hash的初始值为0
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;

            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }

index值，如何生成？这里要求保存的数据是均匀的分配在每一个bucket中，Hashtable源码中采用%操作(mod)使数据分布在编号为0~10的bucket中。
Hashtable中put方法的源码如下：

    private int hash(Object k) {
        // hashSeed will be zero if alternative hashing is disabled.
        return hashSeed ^ k.hashCode();
    }

    public synchronized V put(K key, V value) {
        ... ...
        int hash = hash(key);
        int index = (hash & 0x7FFFFFFF) % tab.length;
        ... ...
    }

这样数据存储到哈希表之后，当我们要查找或者说获取一个对象时候，采用同样的方式可以快速的找到我们需要的对象。

哈希表可以快速的找到一个元素。在有大量的数据的时候，比普通的顺序查找要快的多。
假设有10000条数据，如果采用顺序查找，最坏的情况下需要对比10000次能找到，最好的情况是1次。平均查找次数位(10000+1)/2，大约为5000次。
换一种方式，如果把10000条数据通过hash值索引分成10组，每一组有1000条数据，这样每一次只需要先确定是哪一组，然后在1000条数据里查找，这样最坏的情况是1000次， 最好的情况是1次。平均查找次数为(1000+1)/2 ，大约为500次。比上面的方法快了5倍。

我们[常用的5种算法](http://www.cnblogs.com/butyoux/archive/2013/01/15/2861291.html)有顺序查找，二分法查找，二叉排序树查找，哈希表法查找，分块查找。Java的Hashtable即是用了哈希表法查找。
