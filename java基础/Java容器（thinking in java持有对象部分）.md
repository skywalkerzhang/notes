### Java容器（thinking in java持有对象部分）

* Collection： List Set Queue
* Map



Arrays.asList()可以将数组或者逗号分割的一组元素转为List，但是请注意，不可以add或者remove元素，因为底层还是数组。

Collection.addAll()是接受一个collection参数，collections.addAll()是接受多个可变参数。



#### List操作

* contains()判断**对象**是否在列表中（注意对象和**同一个类的不同实例**的区别）。containsAll()判断括号里的所有对象是否都在这个列表中。
* remove(Object o) 用来删除对象，如果只知道下标那就先用indexOf()把对象找到。
* 确认对象是否属于List会用到equals()方法。
* subList()方法可以获取到List的片段，`pets.subList(1, 4)`。就是指在pets这个列表中下标1,2,3的元素
* Collections.sort()对元素排序
* Collections.shuffle(Random rand)打散数据
* retainAll()两个List求交集。`A.retainAll(B)`返回的是A和B两个List里的交集元素
* removeAll()也基于equals()，删除里面传入的元素列表
* isEmpty()判空，clear()清空
* toArray() 把Collection类型转为数组类型，返回Object数组，但是如果你传递了参数，它可以返回指定类型的数据，如果参数数组类型太小，会创建一个有合适尺寸的数组。`Pet[] p = pets.toArray(new Pet[0])`，这里 pets有超过一个元素，故pet[0]小了，toArray()自己创建了个有合适尺寸的数组。



#### 迭代器

* 采用迭代器**设计模式**的原因：从高层考虑，有时你会对collection的某一子类或者map做相同的迭代操作，从复用代码的角度看，使用一个不需要知道具体类型的迭代器会很方便。它是**轻量级对象**
* Java的Iterator只能单向移动。 
  * 使用iterator()会返回一个Iterator，会**准备**好返回的第一个元素
  * next()获取下一个元素
  * 使用hasNext()来检查有没有元素
  * remove()将迭代器最新近的返回元素删除，要先next()后remove()，在AbstractList里面，next会返回当前对象并把cursor++，remove会remove最后一次返回的，并cursor--。也呼应了前面的“准备”，用next才是指向了元素。
* ListIterator可以双向移动，还可以调用listIterator(n)返回一个开始就指向列表索引n的元素处的ListIterator



#### LinkedList

* getFirst()和element()完全一致，都是返回链表的第一个元素如果为空会报NoSuchElementException.
* peek()也是返回头，但是如果是空直接返回null，不会报异常
* remove()和removeFirst()完全一致，删除并返回链表的头，如果不存在头抛出异常
* poll()返回头，如果不存在头则返回null.
* addLast()和offer()和add()一致，某个元素插入尾部。
* removeLast()移除并返回最后一个元素



#### Stack

* push() 从头部插入元素，和addFirst()一致
* 建议用LinkedList。虽然确实Stack也可以实现



#### Set

Set就是Collection，实现了所有Collection的接口，只是行为不同。

HashSet存取快， TreeSet使用字典序排序（做算法题会用到



#### Map

* containsKey()和containsValue()判断有没有键和值
* keySet()可以返回所有键组成的集合



#### Queue

* 操作offer(),poll(),remove(),peek(),element()同LinkedList

* 优先队列PriorityQueue:

  * 声明下一个元素是按照某种排列规则里最需要的元素，拥有最高的优先级
  * 调用offer()方法添加元素时，这个对象会在队列里被排序，排序规则可以通过Comparator来修改

  ```java
  stringPQ = new PriorityQueue<String>(str.size(), Collections.reverseOrder());
  ```

  



#### Collection & Iterator

在类没有继承时，想使用collection的特性，可以继承AbstractCollection，已经有Collection的默认实现了。但即使这样，还是需要强制实现iteration()和size()

当你无法继承AbstractCollection时，可以考虑实现Iterator。

```java
class PetSequence {
    protected String[] pets = new String[]{"cat", "dog", "bird"};
}

public class NonCollectionSequence extends PetSequence{
    public Iterator<String> iterator(){
        return new Iterator<String>(){
            private int index = 0;
            public boolean hasNext(){
                return index < pets.length;
            }
            public String next() {return pets[index ++];}
            public void remove(){
                throw new UnsupportedOperationException();
            }
        };
    }
}
```



foreach可以用于迭代任何一个Collection对象。能工作是因为Java SE5引入了Iterable接口。接口内部有一个可以产生Iterator的iterator()方法，Iterator在foreach语句中来回移动，因此Iterable类都可以被foreach遍历 。



##### foreach遍历Map

entrySet()产生一个由Map.Entry构成的集合，并且这个集合是Iterable的，可以用于foreach

```jade
for(Map.Entry entry : m.entrySet()){
	entry.getKey();
	entry.getValue();
}
```

foreach可以遍历数组，但是不代表数组是Iterable的，他是基本类型。**如果写代码时把数组当作Iterable的参数传进去会报错**。



适配器方法：当某一类的迭代方式无法改变时，我们可以写一个类继承这个无法改变的类，创建自己的迭代方法，然后利用适配器来调用。

Array,asList(x)后对x生成的列表进行操作会修改x内元素的值。

