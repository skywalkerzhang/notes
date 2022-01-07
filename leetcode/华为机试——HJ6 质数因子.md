### 华为机试——**HJ6** **质数因子**

## 描述

功能:输入一个正整数，按照从小到大的顺序输出它的所有质因子（重复的也要列举）（如180的质因子为2 2 3 3 5 ）

数据范围： ![img](https://www.nowcoder.com/equation?tex=1%20%5Cle%20n%20%5Cle%202%20%5Ctimes%2010%5E%7B9%7D%20%2B%2014%20%5C)

### 输入描述：

输入一个整数

### 输出描述：

按照从小到大的顺序输出它的所有质数的因子，以空格隔开。最后一个数后面也要有空格。

## 示例1

输入：

```
180
```

复制

输出：

```
2 2 3 3 5
```

这道题写是好写的，但是会超时，开始还把它和素数筛搞混了以至于简单题目复杂化了。

这道题有个坑是不用判断除数是不是素数，因为如果你先除以了素数，后面的不是素数的数自然不会被整除，可以少写循环

```java
import java.util.*;
public class Main{
    public static void main(String[] args){
        Scanner s = new Scanner(System.in);
        int x = s.nextInt();
        while(x % 2 == 0){
            x /= 2;
            System.out.print(2 + " ");
        }
        long end = (long)Math.sqrt(x);
        for(int i = 3; i <= end; i += 2){ // 确认是奇数了 右边界可以取到 x一直在变小 // 不用判除数， 从小到大开始除的
            while(x % i == 0){
                x /= i;
                System.out.print(i + " ");
            }
        }
        if(x != 1) System.out.print(x);
    }
}
```

