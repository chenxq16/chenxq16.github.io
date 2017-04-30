---
layout: post
title:  Android中SparseArray<E>优化集合类详解
date:   2016-05-29 12:20:11
tags:
    - android
---

android中集合类的替代方案


* * *
SparseArray使用Integer作为key来映射一个对象。比一般的数组对象集合更为节省内存，旨在提供一个更高效的数据集合来替代HashMap在android中的使用。

SparseArray即称[稀疏数组](http://blog.csdn.net/dzweather/article/details/8057834)，从文中可以很明显的了解到该数据集合类对象相对传统的数组集合来说，可以节省出相当一部分内存。

下面我们看一下该类提供的[API](http://android.toolib.net/reference/android/util/SparseArray.html)如下:
### 添加：

```
void append (int key, E value)
void put (int key, E value)
```

如果key存在，则会执行Update。
这里一般要提一下它的源码说明一下情况如下：

```
/**
* Adds a mapping from the specified key to the specified value,
* replacing the previous mapping from the specified key if there
* was one.
 */
public void put(int key, E value) {    
  int i = ContainerHelpers.binarySearch(mKeys, mSize, key);    
  if (i >= 0) {        
    mValues[i] = value;   
  } else {        
    i = ~i;       
    if (i < mSize && mValues[i] == DELETED) {            
       mKeys[i] = key;           
       mValues[i] = value;            
       return;       
     }        
    if (mGarbage && mSize >= mKeys.length) {            
        gc();            // Search again because indices may have changed.           
        i = ~ContainerHelpers.binarySearch(mKeys, mSize, key);        
    }        
    mKeys = GrowingArrayUtils.insert(mKeys, mSize, i, key);         
    mValues = GrowingArrayUtils.insert(mValues, mSize, i, value);        
    mSize++;    
  }
}
```
大致是二分法查找是否有对应的key值。有就更新，无则添加。并按从小到大排序。

### 删除：
```
void delete (int key)
void remove(int key) 
```
remove调用的是delete方法。

```
void removeAt(int index)
```
<strong>注意参数的区别</strong>

```
void clear()
```
清空所有value

在高版本的API中，还提供了方法
<strong>removeAtRange(int index, int size)</strong> 清空一段范围内的value

### 修改：

```
void setValueAt (int index, E value)

```
该方法必须保证其索引处存在key值，否则无效。<strong>注意此处是index，而不是integer类型的key值，需注意越界</strong>
e.g.

```
  Book book1 = new Book("book1",1);
  Book book2 = new Book("book2",2);
  Book book3 = new Book("book3",3);
  SparseArray<Book> spraseArray = new SparseArray<>(10);
  spraseArray.append(1, book1);
  println(spraseArray);
  spraseArray.setValueAt(0, book2);
  spraseArray.setValueAt(1, book3);
  println(spraseArray);
```
输出信息如下：

```
08-30 10:29:54.577: I/System.out(21088): ----------------sparseArray.size() ＝ 1
08-30 10:29:54.577: I/System.out(21088): key = 1 , obj = Book [name=book1, price=1]
08-30 10:29:54.577: I/System.out(21088): ----------------sparseArray.size() ＝ 1
08-30 10:29:54.577: I/System.out(21088): key = 1 , obj = Book [name=book2, price=2]
```
可见代码<strong>spraseArray.setValueAt(1, book3);</strong>未起到任何作用。有兴趣的可以查看源码，只会修改index对应的value值，不会去管是否有integer key的映射关系存在。

别忘了,<strong>append put</strong>也都可以达到更新的目的。

### 查询：
```
E get(int key)
E get(int key, E valueIfKeyNotFound)
```
方法<strong>E get(int key, E valueIfKeyNotFound)</strong>,其中valueIfKeyNotFound参数意为key对应的value找不到就使用其作为默认值。

```
E valueAt(int index)
```
<strong>一样注意参数为index非key</strong>

### other

```
int size()
```
长度

```
int keyAt(int index)
```
返回key值，可用于遍历
e.g.

```
 private void println (SparseArray sparseArray) {
    System.out.println("----------------sparseArray.size() ＝ " + sparseArray.size());
    int key = 0;
    for (int i = 0; i < sparseArray.size(); i++) {
       key = sparseArray.keyAt(i);
       Object obj = sparseArray.get(key);
       System.out.println( "key = " + key + " , obj = " + obj );
    }
 }
```
以下方法自己脑补场景吧，基本上从方法名可以做到见名知意。

```
int indexOfKey(int key)
```
<strong>找不到为负值，所以自己在定义integer key的时候尽量不要使用负值，以免产生混淆</strong>

```
int indexOfValue(E value)
```

* * *
类似的类还有<strong>SparseIntArray SparseBooleanArray SparseLongArray</strong>可用来替代基本类型组成的数组，到达更高效、更节省内存的目的。enjoy it!
