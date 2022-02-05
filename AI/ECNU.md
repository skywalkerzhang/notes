### ECNU

### 传统

* 句间（对）关系：字符/单词/序列的重叠、语法结构、对齐和机器翻译度量

  *  N-gram Overlaps:

    【N-gram正是基于这样的想法，它的第一个特点是某个词的出现依赖于其他若干个词，第二个特点是我们获得的信息越多，预测越准确。我想说，我们每个人的大脑中都有一个N-gram模型，而且是在不断完善和训练的。我们的见识与经历，都在丰富着我们的阅历，增强着我们的联想能力。】

    [自然语言处理NLP中的N-gram模型_songbinxu的博客-CSDN博客_n-gram](https://blog.csdn.net/songbinxu/article/details/80209197)

    原始单词、原始词形态的单词、字符级别的n gram

    其中n={1,2,3}用于单词级别，n={2,3,4,5}用于字符级别。最后，我们收集了**10**个特征。

  * 翻译特征：
    计算每个句子对的最长公共前缀/后缀/子字符串/序列和levenshtein距离

    【levenshtein距离（编辑距离）：就是计算两个字符串之间的改动次数：改动包括（新增，修改，删除）

    （1-两个字符所有的改动次数 / 两个字符之和 ） = 近似比率】

    删除了stop words, 每个字都被线性化，以便更准确地估计序列相似性。因此，我们得到了**5**个特性。 

  * 句法分析特征：

    tree kernels：https://blog.csdn.net/u010454729/article/details/50848187

    计算了三个不同核空间中公共子结构的数量，即子树（ST）、子集树（SST）、部分树（PT）。

  * 对齐特征（Alignment Features）：[Feature Extraction Techniques - NLP - GeeksforGeeks](https://www.geeksforgeeks.org/feature-extraction-techniques-nlp/)

    要给不同的词赋予适当的权重，我们采用了两种加权方法：i）用五个词性标记（即名词、动词、形容词、副词等）加权；我们首先将两句话中的单词分成五个词性类别，然后对每个词性类别计算对齐单词的比例，得到五个特征。ii）用IDF值加权（在每个数据集中分别计算）。我们总共收集了7个对齐特征。

  * 基于机器翻译的特征

    我们使用机器翻译评估指标来衡量给定句子对的语义等价性。

    9个指标：MT metrics (i.e., BLEU, GTM-3, NIST, -WER,-PER, Ol, -TERbase, METEOR-ex, ROUGE-L are used to assess the similarity. These 9 MT based features are calculated using the Asiya Open Toolkit2

  * 34个特征

* 单句关系：

  匹配特征直接估计两个句子之间的匹配分数不同，单句子特征是在同一向量空间中表示每个句子，以计算句子的相似性。我们设计了以下三种类型的功能。

  * BOW：BOW) and each word (i.e., dimension) is weighted by its IDF value

  * 依赖关系：依赖树是三元组 （label, gov, sub）

  * word embedding features：每个句子都由词的向量表示的最小/最大/平均池连接而成。请注意，对于每个单词，其向量由其IDF值加权。表1列出了这项工作中使用的四种最先进的预训练单词嵌入。

    ![image-20220205202124212](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220205202124212.png)

  * 然而，与句子对匹配特征的数量（33个特征）相比，单个句子特征的维数非常大（大约超过71K个特征），因此会抑制句子对匹配特征的辨别能力。

  * 因此，为了降低单句特征的高维性，我们对每个单句特征使用11个核函数来计算句子对的相似性。 

  * ![image-20220205202313067](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220205202313067.png)

  * 共67个

* 回归方法

  研究了五种回归学习算法，即随机森林（RF）、梯度提升（GB）支持向量机（SVM）、随机梯度下降（SGD）和XGBoost（XGB）。特别是，前四种算法在scikit  learn  toolkit3中实现，XGB在xgboost4中实现。在初步实验中，SVM和SGD的性能低于其他三种算法，因此我们在接下来的实验中采用了RF、GB和XGB

  

  ### 深度学习

  （1） 通过简单地平均单个句子中的单词向量；（2） 在（1）之后，将得到的平均矢量乘以投影矩阵；（3）  通过使用由多层以及非线性激活函数组成的深度平均网络（DAN，Iyyer et al.（2015））； （4）  通过使用长-短期记忆网络（LSTM、Hochreiter和Schmidhuber（1997））捕获长距离依赖信息。 

  过了层DL网络，首先使用元素减法和乘法，然后将这两个值连接起来作为句子对表示的最终向量。最后，我们使用一个完全连接的神经网络，并基于softmax函数输出相似性概率。因此，我们获得了4个基于深度学习的分数。为了了解模型参数，我们最小化了输出和黄金标签之间的KL差异，如Tai等人（2015年）所述。我们采用Adam（Kingma  and Ba，2014）作为优化方法，将学习率设置为0.01。 

  ### 集成 

  基于NLP的分数和基于深度学习的分数在集成模块中取平均值，以获得最终分数。