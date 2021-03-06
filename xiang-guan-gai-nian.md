---
description: 相关前置概念
---

# 虚拟存储器

## 程序的局部性原理

{% hint style="info" %}
**局部性原理**又表现为：_时间局部性_和_空间局部性_。
{% endhint %}

{% tabs %}
{% tab title="空间局部性" %}
**空间局部性**指的是一旦程序访问了某个存储单元，则不久之后。其附近的存储单元也将被访问。
{% endtab %}

{% tab title="时间局部性" %}
**时间局部性**指的是如果程序中的某条指令一旦执行，则不久之后该指令可能再次被执行；如果某数据被访问，则不久之后该数据可能再次被访问。
{% endtab %}
{% endtabs %}

## 命中率

> 在西电出版的《计算机系统结构（第五版）》李学干的教材中P114，4.1.3 存储体系的性能参数一节下，有关二级存储体系的评价相关概念中有关于_**命中率**的叙述如下。_

命中率H定义为**CPU产生的逻辑地址能在M1中访问到（命中到）的概率**。

命中率可用实验或模拟方法求得，即执行或模拟依组有代表性的程序，若逻辑地址流的信息能在M1中访问到的次数为R1，当时在M2还未调到M1的次数为R2，则命中率_**H=R1/\(R1+R2\)**_。显然命中率与程序的地址流、所采用的地址预判算法及M1的容量都有很大关系。我们总希望越接近于1越好。





