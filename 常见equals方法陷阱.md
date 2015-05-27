---
title: 如何在Java中避免重载equals方法隐藏的陷阱
tags: Java,equals,hashCode
---
### 常见的equals方法陷阱
当equals被重载时，这里有4个会引发行为不一致的陷阱：

 1. 定义了错误的方法签名。
 2. 重载了equals但没重载hashCode。
 3. 建立在会变化的字域上的equals。
 4. 不满足等价关系的equals错误定义。
 

### 陷阱1：定义了错误的方法签名
### 重载了equals但没重载hashCode
### 建立在会变化的字域上的equals
### 不满足等价关系的equals错误定义
### canEquals方法
 
 