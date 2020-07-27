---
title: "关于希尔排序的步长有序性的证明"
subtitle: '一个经常被忽视的值得关注的问题'
layout: post
author: "Hux"
header-style: text
tags:
    - Program Art
---

> [希尔排序](https://baike.baidu.com/item/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F)

希尔排序其实就是加了步长的插入排序

虽然本篇文章要说明的事本质上**与希尔排序无关**，不了解希尔排序**不影响阅读**，我尽量做到简洁，但是它能说明**希尔排序的合理性**。

**这篇文章要说明的是经过排序后，使k步长有序的序列变为h步长有序(h < k)，则序列仍然是k步长有序的**

## 规定
我们称序列A(a<sub>1</sub>....a<sub>i</sub>) k步长有序，若A满足

a<sub>1</sub> <= a<sub>1+k</sub> <= ... <= a<sub>1+nk</sub>

a<sub>i</sub> <= a<sub>i+k</sub> <= ... <= a<sub>i+nk</sub>

其中 k，n均为正整数。   



## 定理：经过排序后，使k步长有序的序列A变为h步长有序(h < k)，则序列A仍然保持k步长有序

我们知道，当h是k的因子，结论显然成立。

当h不是k的因子。我们先证明引理L。
</br></br></br>
**引理L**：若序列X(x<sub>1</sub>...x<sub>m+r</sub>)，Y(y<sub>1</sub>...y<sub>n+r</sub>)，满足

x<sub>1</sub> <= y<sub>n+1</sub>

...

x<sub>r</sub> <= y<sub>n+r</sub>

则分别对X,Y升序排序后，**仍然有上述关系成立**。
</br></br></br>
现在证明引理L：

对X,Y排序后，

首先有y<sub>n+1</sub> <= ... <= y<sub>n+r</sub>

且对y<sub>n+i</sub>(1 <= i <= k)，必定存在x属于序列X，使得x 
<= y<sub>n+i</sub>.

即y<sub>n+j</sub>至少大于j个x

也即 x<sub>k</sub> <= y<sub>n+k</sub>（1 <= k <= r)，引理L得证。

</br></br></br>

若序列A(a<sub>1</sub>...a<sub>i</sub>)是k步长有序的，则有：

a<sub>1</sub>   a<sub>1+k</sub>...   a<sub>1+nk</sub>

a<sub>2</sub>   a<sub>2+k</sub>... a<sub>2+nk</sub>

a<sub>i</sub>  a<sub>i+k</sub>... a<sub>i+nk</sub>
</br></br>

**上述每行都是有序的**

k步长有序的序列A的可表示如下：

a<sub>1</sub>&nbsp;    a<sub>1+h</sub>&nbsp; ...&nbsp;    a<sub>1+nh</sub>

a<sub>2</sub>&nbsp;    a<sub>2+h</sub>&nbsp; ...&nbsp;  a<sub>2+nh</sub>

a<sub>i</sub>&nbsp;   a<sub>i+k</sub>&nbsp; ...&nbsp;  a<sub>i+nh</sub>

现在取它的局部观察

... a<sub>i</sub>&nbsp;  a<sub>i+h</sub> &nbsp; a<sub>i+2h</sub> ... &nbsp; &nbsp; &nbsp;  (1)
</br></br>
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; ... a<sub>i+k</sub> &nbsp; a<sub>i+k+h</sub>&nbsp;  a<sub>i+k+2h</sub> ...&nbsp; &nbsp; &nbsp; (2)

(1)和(2)就是满足引理L的两个序列。(a<sub>i</sub> < a<sub>i+k</sub>)

所以对序列A变为h步长有序后，序列A仍然保持k步长有序。