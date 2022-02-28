### 速通R语言

1. 数据分析过程：数据采集->数据存储->数据统计->数据挖掘->数据可视化

![image-20220221103136723](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221103136723.png)

![image-20220221103207475](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221103207475.png)

2.数据挖掘

数据可视化很重要

3.使用<-赋值，不使用=，否则可能与假设检验中的=混淆，可以向右赋值，但是不推荐。x <<- 5表示强制赋值给一个全局变量，而不是函数变量。

4. ls()用来列出变量，str给常见信息。可以组合ls.str()

![image-20220221115813670](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221115813670.png)

![image-20220221115853488](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221115853488.png)

ls(all.names=TRUE)相当于linux中的ls -a

删除使用rm函数，例如rm(x)就是删除x，删除多个是rm {y,z}，删除所有是rm (list=ls())

5.save.images()可以存下R的变量，省的突然卡死

6.help(package="vcd") 查找帮助， ls("package:vcd")列出vcd里的所有命令。data(package = "vcd")列出vcd里的所有数据集。内存移除detach("package:vcd")。

7.如何大批量备份&下载安装包 https://blog.csdn.net/m0_37149062/article/details/120180251

8.获取帮助，使用help.start().也可以指定包使用help函数help(package=包名)，但是要先载入。或者使用??函数名

9.x <- c(1,2,3,4,5),c表示concat.

10.向量，向量必须是同一类型。

11.x[-19]代表不输出第19个元素

12.![image-20220221170442782](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221170442782.png)

第一个就是按顺序输出/不输出。第二个是会把每个值都输出，第三个是交替输出，就是挨着for，第一个先for true然后False然后true

13.字符串![image-20220221170738027](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221170738027.png)

![image-20220221170912270](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221170912270.png)

![image-20220221171134969](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221171134969.png)

![image-20220221171258730](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221171258730.png)

![image-20220221171348041](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221171348041.png)

14.向量运算

![image-20220221171938814](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221171938814.png)

求余和整除

![image-20220221172204297](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221172204297.png)

一一比对左边和右边



celling返回不小于x的最小整数，floor返回不大于x的最大整数,trunc返回整数部分,round四舍五入

![image-20220221172926741](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221172926741.png)

 *signif*(x,a) : 数据截取函数 x:有效位 a:到a位为止

range返回最大和最小值，prod连*

![image-20220221173236709](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221173236709.png)

![image-20220221173341063](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220221173341063.png)