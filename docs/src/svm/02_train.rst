Train
=====

指定训练数据集
-------------------

.. code-block:: shell

    def load_data():
        k1 = ['dataset/data_float16_v1.npz', 'dataset/data_float16_v3.npz', 'dataset/data_float16_v4.npz']
        oppo = ['dataset/data_float16_v5.npz', 'dataset/data_float16_v6.npz', 'dataset/data_float16_v7.npz']
        files = []
        files.extend(oppo)
        X_train_all, X_test_all, y_train_all, y_test_all = [], [], [], []
        for file in files:
            data = np.load(file)
            X_train, X_test, y_train, y_test = data['X_train'], data['X_test'], data['y_train'], data['y_test']    
            X_train_all.append(X_train)
            X_test_all.append(X_test)
            y_train_all.append(y_train)
            y_test_all.append(y_test)
        X_train_all = np.concatenate(X_train_all)
        X_test_all = np.concatenate(X_test_all)
        y_train_all = np.concatenate(y_train_all)
        y_test_all = np.concatenate(y_test_all)

        X_train_all = enhance_data(X_train_all)
        X_test_all = enhance_data(X_test_all)
        
        return X_train_all, X_test_all, y_train_all, y_test_all

我们首先修改svm_origin.py的load_data函数，这个函数有两个list: oppo和k1，分别代表两种手机的数据集，如果我们新的数据集是k1的数据集，我们可以把代码
改成以下形式: 

.. code-block:: shell

    k1 = ['dataset/data_float16_v1.npz', 'dataset/data_float16_v3.npz', 'dataset/data_float16_v4.npz', 'dataset/data_float16_v8.npz']
    oppo = ['dataset/data_float16_v5.npz', 'dataset/data_float16_v6.npz', 'dataset/data_float16_v7.npz']
    files = []
    files.extend(k1)

训练模型并保存参数
-------------------

.. code-block:: shell

    python svm_origin.py

运行svm_origin.py脚本就会训练对应的SVM模型并将参数保存到model目录中。

训练代码分析
----------------

.. code-block:: shell

    # (1)读取数据
    X_train, X_test, y_train, y_test = load_data()

首先，读取训练数据集的数据。

.. code-block:: shell

    # (2)提取数据特征
    print('Begin Feature Extraction')
    start_time = time.time()
    X_train_feature = feature_extraction(X_train)
    end_time = time.time()
    print(f'{end_time - start_time:.6f}s for {y_train.shape[0]} samples feature extraction')
    print()
    
    start_time = time.time()
    X_test_feature = feature_extraction(X_test)
    end_time = time.time()
    print(f'{end_time - start_time:.6f}s for {y_test.shape[0]} samples feature extraction')
    print()

然后，我们对训练集和测试集的图片分别进行特征提取，调用的是skimage的hog函数。

.. code-block:: shell

    # (3)创建SVM模型
    print('Begin SVM')
    clf = svm.SVC() # 默认kernel为rbf，测试代码仅支持kernel为rbf

.. code-block:: shell

    # (4)训练SVM模型
    clf.fit(X_train_feature, y_train)

然后，我们创建SVM模型并进行训练，调用的是sklearn的SVM模型，实际代码只要两行。

.. code-block:: shell

    # (5)保存SVM模型参数
    LIBSVM_IMPL = ["c_svc", "nu_svc", "one_class", "epsilon_svr", "nu_svr"]
    print('X_test_feature', X_test_feature.shape)
    print('self.support_', clf.support_.shape, clf.support_)
    print('self.support_vectors_', clf.support_vectors_.shape, np.array(clf.support_vectors_, dtype=np.float16))
    print('self._n_support', clf._n_support)
    print('self._dual_coef_', clf._dual_coef_.shape, np.array(clf._dual_coef_, dtype=np.float16))
    print('self._intercept_', np.array(clf._intercept_, dtype=np.float16))
    print('self._probA', clf._probA)
    print('self._probB', clf._probB)
    print('svm_type', LIBSVM_IMPL.index(clf._impl))
    print('kernel', clf.kernel)
    print('self.degree', clf.degree)
    print('self.coef0', np.float16(clf.coef0))
    print('self.gamma', np.float16(clf._gamma))
    print('self.cache_size', clf.cache_size)
    print()

    np.savez('model/params.npz', support = clf.support_, SV = np.array(clf.support_vectors_, dtype=np.float16), nSV = clf._n_support, 
            sv_coef = np.array(clf._dual_coef_, dtype=np.float16), intercept = np.array(clf._intercept_, dtype=np.float16), svm_type = LIBSVM_IMPL.index(clf._impl), 
            kernel = clf.kernel, degree = clf.degree, gamma = np.float16(clf._gamma), coef0 = np.float16(clf.coef0))

    print(clf.break_ties, clf.decision_function_shape, len(clf.classes_), clf._sparse, callable(clf.kernel))
    print()

    save_path = 'model/bin/'
    clf.support_.tofile(save_path+'01_support_int32.bin')
    clf.support_vectors_.astype(np.float32).tofile(save_path+'02_SV_float32.bin')
    clf._n_support.tofile(save_path+'03_nSV_int32.bin')
    clf._dual_coef_.astype(np.float32).tofile(save_path+'04_sv_coef_float32.bin')
    clf._intercept_.astype(np.float32).tofile(save_path+'05_intercept_float32.bin')
    np.array(LIBSVM_IMPL.index(clf._impl)).astype(np.float32).tofile(save_path+'06_svm_type_float32.bin')
    np.array(clf.kernel).tofile(save_path+'07_kernel_int32_string.bin')
    np.array(clf.degree).tofile(save_path+'08_degree_int32.bin')
    np.array(clf._gamma).astype(np.float32).tofile(save_path+'09_gamma_float32.bin')
    np.array(clf.coef0).astype(np.float32).tofile(save_path+'10_coef0_float32.bin')

然后，我们将模型的参数保存到model目录中，方便后续转化为对应的C++头文件。

.. code-block:: shell

    # (6)测试模型精度
    print(' -------- 原版的SVM模型测试 ---------- ')
    start_time = time.time()
    result = clf.predict(X_train_feature)
    end_time = time.time()
    analysis_result(y_train, result, end_time-start_time)

    start_time = time.time()
    result = clf.predict(X_test_feature)
    end_time = time.time()
    analysis_result(y_test, result, end_time-start_time)

最后，我们测试一下测试集的结果，方便判断SVM模型对于这批数据的分类效果是否达到我们的预期。