---
title: python笔记
date: 2026-1-30 20:15:52
categories: 
    - 编程基础
tags: 
    - python
mp3: http://domain.com/awesome.mp3
cover: https://wallpaperaccess.com/full/1672441.png

---

# Python笔记


---

> 前言，python里莫得define，所以很多程序我打算只写一部分，不写全了
> 主要的目的是学和写[[算法]]


---

## 一些基本的**数组/链表**

本笔记中将全部使用**增删改查的方式**展示

**增**: list.append(值)
**删**: list.pop(位置) **不能多选位置？**
list.remove(值)**删除第一个出现的该值** 
del list[0]删除开头**一般给list作为tree或者队列的时候使用**
del可以拓展多选下标
del list[0:2]即为删除下标0-1
**改**: 嘶，直接赋值吧

**查**: list.index(值)**查询第一个出现的**,注意index如果没查到则报错

```python
from collections import deque
dq = deque([1,2,3,4.1])
dq.popleft();dq.appendleft(2)
try:
    print(dq.index(0))
except:
    print("没有")
```



/特殊的查询:if not list(查询是否为空的，算是一种符合ACM模式?)

**排**: list.sort()内置的一种方法，或者新列表 = sorted(列表)**不改变原列表的一种函数**

**反**: list.reverse()<img src="https://so1.360tres.com/t0160370fe30b4fdff7.png" alt="如图所示.png1 (369×552)" style="zoom:33%;" />

**计**: count神教，count(查询数量的值)/len查询个数

---

## 队列(queue / 双端队列 deque)

> 先进先出

**queue**

```python
from queue import Queue
q = Queue()
for i in range(1,10+1):
    q.put(i)
while not q.empty():
    print(q.get(),end=" ")
```



**deque**

```python
from collections import deque
dq = deque([1,2,3,4,5,6])
print(dq)
```

### queue:

**增**: q.put(值)
**删**: q.get顺手的事，输出并移出，**先进先出**
**改**: 应该是不能改
**查**: empty()查看是否为空，如果是空输出True
**排**: 无
**反**: 无
**计**: q.qsize()

### deque

**增**: dq.append(值)在**队尾**增，dq.appendleft(值)在**队头**增，deque中其他的同理
**删**: dq.pop()队尾，dq.popleft()队头
**改**: 赋值
**查**: if dq判断是否为空以及index
**排**: sorted方法
**反**: reverse
**计**: len

## 优先队列

### 也就是最优数据一直位于队头PriorityQueue(其实不算是队列了)

```python
from queue import PriorityQueue
pq = PriorityQueue()
pq.put((10, "低优先"))  
pq.put((5, "中优先"))
pq.put((0, "高优先"))
while not pq.empty():
    curr_priority,curr_value = pq.get()
    print("当前优先级:"+f"{curr_priority}"+'\n'+"当前值:"+f"{curr_value}")
```

**增**: pq.put((优先级,值))

**删**: pq.get()
**改**: 优先级,值=pq.get()
**查**: empty查是否为空
**排**: 自动排好的
**反**: 没有
**计**: pq.\__sizeof__()

## 栈

### 有唯一的出入口 先进后出

一般是LifoQueue，list，deque

```python
from queue import LifoQueue
st = LifoQueue()
for i in range(1,11):
    st.put(i)
while not st.empty():
    item = st.get()
    print(item)
```



### LifoQueue

初始化的时候可以用size来直接限定内容数

**增**: st.put(值)
**删**: pq.get()出栈并读取
**改**: 同增
**查**: 无
**排**: 哒咩
**反**: 哒咩
**计**: st.\__sizeof__()