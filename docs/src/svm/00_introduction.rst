Introduction
============

我们希望能够将一些图片区分开来，比如把图卡类型的图片和其它类型的图片区分开来，这样子可以更好地针对图卡类型的图片进行调参，因此冬哥让我实现了一个SVM来进行这个二分类任务。

SVM的训练部分的代码比较复杂，测试部分的代码比较简单，部署时我们需要使用C++。实际上我们并不需要用C++实现SVM的训练部分，只需要用C++实现SVM的测试部分。

首先，我们可以使用Python调用sklearn等已有的package训练SVM模型，然后把对应的模型参数存下来，最后把模型参数转化为C++的头文件，这样子我们就可以只在C++中实现SVM的测试部分，大大减小了工程量。

具体步骤
--------------

1. Generate Dataset。一般情况下，芦苇会给我们一批数据，我们需要对数据进行分类，比如把图卡类数据统一分到文件夹1，把其它类数据统一分到文件夹0，然后调用生成数据集的脚本生成训练使用的数据集。

2. Train Model。当我们生成数据集之后，我们再调用一下训练脚本训练一下SVM模型并保存参数，一般只需要很短的时间。

3. Generate C++ Header File。最后，我们调用C++代码，将保存下来的SVM模型参数转化为对应的C++头文件，将头文件上传到nas上，再告知芦苇一下就行了。

代码结构
--------------

.. figtable::
    :label: table-xml-objects
    :caption: A high-level overview.

    .. list-table::
        :widths: 17 53 30
        :header-rows: 1

        * - Directory or Script
          - Description
          - example
        * - cpp
          - SVM测试部分的C++代码，如果不关心具体C++实现可以无视
          - None
        * - image
          - README的一些展示图片，可以无视
          - None
        * - model
          - 保存SVM训练的模型参数，以及转化成的C++头文件，以及一个样例C++头文件
          - None
        * - other
          - 保存用于将SVM训练的模型参数转化为C++头文件的C++代码
          - None
        * - util
          - Python工具脚本，主要为文件处理相关
          - None
        * - CMakeLists.txt
          - CMake配置文件
          - None
        * - data2npy.py、dataloader.py
          - 用于生成训练数据集的脚本
          - python data2npy.py
