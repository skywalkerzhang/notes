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

#### 数学

* 小数点保留

```dart
double d = 0.6544;
String s=String.format("%.2f",d);
System.out.println(s);

Math.round() //保留的是整数
```

* 进制转换 
  * 10 -> 多进制
  * 多 -> 10

```java
//使用java提供的方法
  //但仅局限于比较常用的二进制、八进制、十六进制
  public static String trans1(int num, int radix)
  {
    if(radix == 2)
      return Integer.toBinaryString(num);
    else if(radix == 8)
      return Integer.toOctalString(num);
    else if(radix == 16)
      return Integer.toHexString(num);

    return null;

  }
```

```java
System.out.println(Integer.valueOf(str, 16));
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

#### 排序

这里直接踩坑，java Arrays.sort很难对一维数组从大到小排序，不行还是先转Integer[]吧，因为int没有继承自object方法

```java
Arrays.sort(); // 里面可以放int数组
// int数组从大到小排序
// 用for先转为Integer数组
Arrays.sort(g, (a1, a2) -> (a2 - a1));
Arrays.sort(s, (a1, a2) -> (a2 - a1));

lst.sort((l1, l2) -> l1.compareTo(l2)); // 里面是个lambda表达式，用于写排序规则
```

但是二维数组就可以啦，因为对于int\[][]a来说 b=int[]是继承自object类型的喔

```java
Arrays.sort(people, (a, b) -> {
    if (a[0] == b[0])               
        return a[1] - b[1];
    return b[0] - a[0];
});
```

#### 容器

* Map 迭代

```java
for (Map.Entry<String, Object> entry : infoMap.entrySet()) {
    System.out.println(entry.getKey() + "：" + entry.getValue());
}
```

