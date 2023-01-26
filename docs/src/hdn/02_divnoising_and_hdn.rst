DivNoising and HDN
==================

DivNoising
--------------

1. VAE原本是一个用于图像生成的模型，怎么将其用于降噪？关于这个问题需要看一下 `DivNoising的论文 <https://github.com/liudeyuan2021/BoringSRDoc/tree/master/resources/paper/Full_Unsupervised_Diversity_Denoising_With_Convolutional_Variational_Autoencoders.pdf>`_。

2. DivNoising的论文有非常多页，我感觉读前几页了解一下怎么将VAE用于降噪即可。

3. DivNoising的原理与VAE基本一致，主要区别是引入了一个噪声模型让VAE可以直接生成去噪的图片，可以用于无监督的降噪任务。


HDN
--------------

1. HDN是在DivNoising的基础上在多个图像尺度上进行了操作，具体内容可查看 `HDN的论文 <https://github.com/liudeyuan2021/BoringSRDoc/tree/master/resources/paper/Interpretable_Unsupervised_Diversity_Denosing_And_Artefact_Removal.pdf>`_。

2. 另外 `HDN的代码仓库 <https://github.com/juglab/HDN>`_，提供了非常多的用例，可以运行一下其中的Pixel Noise的两个example，代码行数也非常少。

3. HDN的噪声模型主要由两种，一种是Histogram Model，一种是Guassian Mixture Model。想要了解噪声模型的原理看HDN代码仓库中的histNoiseModel.py和gaussianMixtureNoiseModel.py即可，代码行数不多，在理解了VAE以及DivNoising引入的噪声模型的作用后，看这些代码可以很快理解。