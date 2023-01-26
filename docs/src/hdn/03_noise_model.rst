Noise Model
===========

在研究了HDN的论文和代码后，目前的主要思路是将HDN的Guassian Mixture Noise Model引入到我们的降噪模型中，看看是否能提升降噪的性能。

我们的Noise Model和HDN的Noise Model的区别主要是:

1. 我们的Noise Model是Guassian Model，HDN的Noise Model是Guassian Mixture Model。

2. 我们的Noise Model考虑了ISO、Pixel Intensity，而HDN的Noise Model只考虑了Pixel Intensity。

3. 我们的Noise Model的Guassian Mean等于Pixel Intensity，而Var随着ISO和Pixel Intensity变化。而HDN的Noise Model的Guassian Mean和Var都随着Pixel Intensity变化。

我觉得如果能更好地给训练图片加上噪声，那么降噪模型的效果也会更好，可以分别用我们的Noise Model和HDN的Noise Model进行降噪网络的训练，看看是否有提升。

由于HDN的Noise Model未考虑ISO，我们可以将其扩展为考虑ISO或者只先测试某个ISO。