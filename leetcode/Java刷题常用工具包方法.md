### Java刷题常用工具包/方法

以前经常用c++做题的人的笔记，以及踩了n次的坑门，持续更新 TAT

#### 输入输出

使用`import java.util.*`下的Scanner类。如果不知道输入多少的话采用hasNext()在while循环里判断

```java
Scanner s = new Scanner(System.in);
while(s.hasNext()){
    int n = s.nextInt();
    String str = s.nextLine(); // 否则读到空格就停啦
}
```

#### 字符串处理

* split（实现可以参照随想录 **剑指Offer 05.替换空格**和**[151. 翻转字符串里的单词](https://leetcode-cn.com/problems/reverse-words-in-a-string/)**）

```java
String str = "hello world";
String[] res = str.split();
```

* 翻转

 利用 [StringBuffer](https://so.csdn.net/so/search?q=StringBuffer) 或 StringBuilder 的 reverse 成员方法:

```java
StringBuilder(str).reverse().toString();
```

### 排序

```java
Arrays.sort(); // 里面可以放int数组
// int数组从大到小排序
Arrays.sort(g, (a1, a2) -> (a2 - a1));
Arrays.sort(s, (a1, a2) -> (a2 - a1));

lst.sort((l1, l2) -> l1.compareTo(l2)); // 里面是个lambda表达式，用于写排序规则
```

