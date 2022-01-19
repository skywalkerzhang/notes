### BERT

keywords:Pre-training, deep, bidirectional

BERT是基于两个模型改进过来的，一个是ELMo，一个是GPT。

GPT是一个单向的transformer,利用左边的信息预测右边的信息，在迁移学习中属于fine tuning流派

ELMo是一个基于RNN架构的双向网络，针对不同的下游问题调整相应的网络结构

语言任务主要有两个层面：1. 句子层面的语言任务：eg 句子情感，句子关系等  2.词元（token）层面：实体命名识别

现在大家习惯于把NLP领域的pretraining归功于BERT,但BERT不是第一个有pretraining的网络。

pretraining有两种策略

* feature-based：ELMo

  对每个下游的任务，构造与任务相关的RNN，将得到的表示作为新的输入和原输入一并并入模型

* fine tuning-based: GPT

  对于下游任务，基本的模型架构不会变，只会有一些微调来适应不同问题。

一般来说，传统的语言模型是单向的，由前面的词**预测**后面的词（比如翻译）

但是本文作者认为，单向尤其局限性，对于句子情感分析之类的任务，显然通过上下文判断更为准确。对于一些问题放入双向信息，可以提高性能。

这种双向的实现是基于Mask的，我们可以选择句子里的一个词把它盖上，像做完形填空一样从前后词得到盖住的词。

还可以随机采样句子来判断两个句子是否相邻。

大体上可以说，BERT是一个双向transformer，基于fine tuning的 开源 模型 。



#### BRET实现

BERT的pre-training是无监督的，在下游任务fine-tuning的时候会将BERT模型权重初始化为pre-training模型得到的参数，然后调这些参数。并为不同类型的下游任务创建新的模型。

可调参数

* L -- transformer块的个数 
* H -- hidden layer的大小
* A self attention机制中head的个数

BERT base版本 L = 12, H = 768, A = 12

BERT large版本 L = 24, H = 1024, A = 16

输入：句子或者句子对，transformer是需要输入句子对的，但是BERT采用了新的机制，利用[SEP]作为句子的分割，因此可以输入一段文字。

* wordpiece： BERT的处理技巧，当一个词不常出现时，把他拆分为常出现的部分和剩余部分 eg playing -> play ##ing
* CLS：表示整个序列的信息
* SEP：句子分割用的

![架构](D:\notes\paper\bert20190926153602.png)

可以从上图看到预训练和下游任务的模型架构，主要区别就是有无MASK和NSP

每个词元进入BERT后的向量表示如下图

![](D:\notes\paper\bert20190926180722.png)

Token Embeddings是词元信息，segment 是代表这个词属于哪个句子，position是BERT自己学习来的位置向量



##### pre-training

在pre-training阶段，word pieces有15%的概率被换成MASK，但是如果换成mask，会和fine tuning阶段看起来不太一样，因为fine tuning没有mask

他们最后在这15%中选取80%概率为MASK, 10%换为其他词，10%保持原状

​			My dog is hairy

MASK My dog is [MASK]

随机词元 My dog is apple

保持 My dog is hairy

除了MASK外，pre training还用了另一个技巧叫NSP（Next Sentence Prediction）

令两个句子A,B 中B在原文是A之后的概率是50%。是随机其他位置的概率是50%。用label isNext表示，这样就有50%的正样本和负样本

实验中数据集来源于 BooksCorpos和 English Wikipedia



###### fine tuning

之前我们讲过，BERT是看左右信息的，但是一般，encoder是不应该看到decoder里的信息的，所以BERT不能够作为机器翻译

当任务的输入涉及到AB两个句子时，只需按照正常的模型使用即可。

但有时候输入只有一个句子，目标可能是对句子做分类，或者得到词元的输出，这时就应当使用softmax层在进行输出。



#### Experiments

1.GLUE：句子层任务，作者拿出[CLS] 学习它的输出得到softmax标号

2.SQuAD QA问题，在原文中选出问题的答案

处理方法是针对词元计算他是开头或者结尾的概率

3.SWAG 判断句子间的关系