### ViT Vision Transformer

首先讲下 ViT 在以下四种场景内表现很优秀：

* 图片遮挡
* 分布偏移
* 某部分加入对抗Patch
* 图片切割成子图后做排列组合

在文章的Abstract中强调，它可以使用Transformer做大规模的图像识别，并且不依赖于CNN。达到其他模型的同等效果只需更少的资源。

整篇文章可以说是由BERT启发的，主要就是把CV任务构造成和NLP一样的任务处理。之前有提到过Transformer目前还没有出现性能饱和的情况（在更多参数或者更大模型上有性能瓶颈）。因此理论上Transformer出世后应该会应用到CV，但是之前一直没有特别火的原因是文本是1D数据，图像是2D，当时还没有找到高效的2D -> 1D的方法。

最简单的2D -> 1D的方法就是把图像按照每个像素点展平，在分类任务中一般使用224*224的图像，展平后就是50176，作为输入实在是太大了，大约是BERT的一百倍。之后，有科学家提出可以用stand alone方法，使用一个local window传图。还有人提出可以使用轴注意力分别在W和H两个维度上self attention。包括还有一些spare attention方法。

但是以上的方法的attention都比较特殊，不能GPU加速。ViT希望就用标准的Transformer解决CV问题，尽量少的改进模型。

首先，他把图像切割成多个16 * 16的patch，把每个patch当作一个元素，通过FC层做linear embedding，然后放入transformer。

这里他们把patch当作文本里的单词，一个句子有多少词就对应这里一个图像有多少patch。

我们知道，BERT在预训练的时候是无监督的，但是这里还是有监督的（后文做了无监督的实验，效果不算太好，但是MAE补上了这块。）

通过实验，他们发现，ViT在中小规模上数据表现不如CNN，他们认为是因为没有足够的先验知识(inductive bias)

CNN的先验知识有两个 1. localing 2. translation equivariance。

此外，在VTAB数据集上模型效果不错，证明ViT是比较稳定的模型。（VTAB可以用于测试模型的稳定性）

ViT因为把CV和NLP一统了，所以可以考虑多模态任务也可以用transformer解决。



#### some details

* 图片预处理

对于图片而言，patch是有序的，因此在对patch做嵌入的时候需要加上位置信息。受BERT启发，他们也用[CLS]来获得整体信息。

以实验为例，做分类任务的图片X = 224* 224 *3

取16 * 16做Patch，那么patch就有 224 *224 / 16 * 16 = 196个

全连接层的大小是 768 * 768.前面的768根据图片大小来的，后面是论文里提到的D，是他们设定的。然后经过FC你会发现patch就变成了196 *768 的1D向量。按NLP的话说就是196 个token。这里注意下它的位置信息是sum，直接加法，而不是concat拼接的。

![image-20220120212351631](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220120212351631.png)

然后看右边的transformer，这里在Embedded Patches维度是197 * 768，然后Norm后也是197 * 768.之后进入多头自注意力部分会被等量拆分为三份，因此是Q, V, K 每个都是197 * 64，然后concat下回复197 * 768.MLP是197 * 768 ->  197 * 3072 -> 197 *768。 因此你发现encoder里维度没变，所以就可以随便叠N层。

然后刚提到他这里的位置信息是按照BERT一样自学习编码的，然后有的人会思考了，图片是2D的，是不是整个2D编码更好？实际上作者试了，还试了相对位置编码，发现1D就行了，效果很好。为啥呢？其实因为1D自学习编码已经学出2D信息了。见下图

![image-20220120212946928](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220120212946928.png)

然后他又研究了下CNN + Transformer发现在中小规模混合模型跑的好，大规模数据还得看ViT（略好于混合/或者说持平）

![image-20220120213131590](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220120213131590.png)

然后他们研究了下可解释性，拿到了第一层的主成分，把它们前28个可视化了，长得非常像CNN提取出来的特征图

![image-20220120213228544](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220120213228544.png)

也直接输出了attention 得到的输出可视化 和原图的对比，不得不说学的很好

![image-20220120213320559](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220120213320559.png)

然后再实验中还证明了attention确实学的全局

![image-20220120213416480](C:\Users\rina\AppData\Roaming\Typora\typora-user-images\image-20220120213416480.png)

In words,他们挖了几个坑 1. transformer CV 多模态很香 2. 小样本Transformer有前途 3. 可不可以无监督预训练？