.. _text_data_tutorial:

======================
使用文本数据
======================

本指南旨在一个单独实际任务中探索一些主要的 ``scikit-learn`` 工具: 分析关于20 个不同主题的一个文件汇编（新闻组帖子）.

在本节中，我们将会学习如何:

  - 读取文件内容以及所属的类别

  - 提取合适于机器学习的特征向量

  - 训练一个线性模型来进行分类

  - 使用网格搜索策略来找到特征提取组件以及分类器的最佳配置



教程设置
--------------

开始这篇教程之前，必须首先安装 *scikit-learn* 以及所有其要求的库.

更多信息和系统安装指导请参考 :ref:`安装说明 <installation-instructions>` .

这篇入门教程的源代码可以在您的 scikit-learn 文件夹下面找到::

    scikit-learn/doc/tutorial/text_analytics/

这个入门教程应该包含以下的子文件夹:

  * ``*.rst files`` - 用 sphinx 编写的该教程的源代码

  * ``data`` - 用来存放在该教程中用到的数据集的文件夹

  * ``skeletons`` - 用来练习的未完成的示例脚本

  * ``solutions`` - 练习的答案


你也可以将这个文件结构拷贝到您的电脑的硬盘里名为 ``sklearn_tut_workspace`` 的文件夹中来编辑你自己的文件完成练习，同时保持原结构紧凑::

    % cp -r skeletons work_directory/sklearn_tut_workspace

机器学习算法需要数据.
进入每一个 ``$TUTORIAL_HOME/data`` 子文件夹，然后运行 ``fetch_data.py`` 脚本（在您已经事先读取它们之后）.

例如::

    % cd $TUTORIAL_HOME/data/languages
    % less fetch_data.py
    % python fetch_data.py


加载这20个新闻组的数据集
---------------------------------

该数据集名为 "Twenty Newsgroups".
下面就是这个数据集的介绍, 来源于 `网站 <http://people.csail.mit.edu/jrennie/20Newsgroups/>`_:

  20 个新闻组数据集是一个近 包括了20,000 个新闻组文件的汇编，（几乎）平均分成了 20 个不同新闻组.
  据我们所知，这最初是由 Ken Lang 收集的 ，很可能是为了他的论文 "Newsweeder: Learning to filter netnews," 尽管他没有明确提及这个汇编.
  这20 个新闻组汇编已成为一个流行的数据集，用于机器学习中的文本应用的试验中，如文本分类和文本聚类.

接下来我们会使用 scikit-learn 中的这个内置数据集加载器来加载这20个新闻组.
或者, 也可以手动从网站上下载数据集，然后通过指向未压缩的文件夹下的 ``20news-bydate-train`` 子文件夹来使用函数 :func:`sklearn.datasets.load_files` .

为了在第一个示例中节约时间，我们将使用部分数据：从20类别中选出4个::

  >>> categories = ['alt.atheism', 'soc.religion.christian',
  ...               'comp.graphics', 'sci.med']

如下所示，我们现在能够加载包含对应类别的文件的列表::

  >>> from sklearn.datasets import fetch_20newsgroups
  >>> twenty_train = fetch_20newsgroups(subset='train',
  ...     categories=categories, shuffle=True, random_state=42)

返回的数据类型是一个 ``scikit-learn`` "bunch": 一个简单的包含多个 "field" 的存储对象 ， 方便使python 中的 ``dict`` keys 或 ``object`` 中属性来读取, 比如 ``target_names`` 包含了所要求的类别名称::

  >>> twenty_train.target_names
  ['alt.atheism', 'comp.graphics', 'sci.med', 'soc.religion.christian']

这些文件本身被读进内存的 ``data`` 属性中.
另外，这些文件名称也可以容易获取到::

  >>> len(twenty_train.data)
  2257
  >>> len(twenty_train.filenames)
  2257

让我们打印出所加载的第一个文件的前几行::

  >>> print("\n".join(twenty_train.data[0].split("\n")[:3]))
  From: sd345@city.ac.uk (Michael Collier)
  Subject: Converting images to HP LaserJet III?
  Nntp-Posting-Host: hampton

  >>> print(twenty_train.target_names[twenty_train.target[0]])
  comp.graphics

监督学习需要让训练集中的每个文档对应一个类别标签.
在这个例子中，类别是每个新闻组的名称，也刚好是每个储存文本文件的文件夹的名称.

由于速度和空间上效率的原因 ``scikit-learn`` 加载目标属性为一个整型数列,
它与 ``target_names`` 列表中类别名称的 index（索引）相对应.
每个样本的类别的整数型 id 存放在 ``target`` 属性中::

  >>> twenty_train.target[:10]
  array([1, 1, 3, 3, 3, 3, 3, 2, 2, 2])

也可以通过如下方式取得类别名称::

  >>> for t in twenty_train.target[:10]:
  ...     print(twenty_train.target_names[t])
  ...
  comp.graphics
  comp.graphics
  soc.religion.christian
  soc.religion.christian
  soc.religion.christian
  soc.religion.christian
  soc.religion.christian
  sci.med
  sci.med
  sci.med

你可以发现所有的样本都被随机打乱（使用了修正的 RNG 种子）: 当你在训练进行整个数据集之前，你只要选取前几个样本来快速训练一个模型的时候以及获得初步结果.这是非常有用的.


从文本文件中提取特征
-----------------------------------

为了在文本文件中执行机器学习算法, 我们首先要做的是将文本内容转化成数值形式的特征向量.

.. currentmodule:: sklearn.feature_extraction.text


词袋
~~~~~~~~~~~~~

最直观的方法就是用词袋来表示:

  1. 在训练集中每一个出现在任意文中的单词分配一个特定的整数id（比如，通过建立一个从单词到整数索引的字典）.

  2. 对于每个文档 ``#i``，计算每个单词 ``w`` 的出现次数并将其存储在 ``X[i, j]`` 中作为特征 ``#j`` 的值，其中 ``j`` 是在字典中词 ``w`` 的索引.

在这种方法中 ``n_features`` 是在整个文集（文章集合的缩写，下同）中不同单词的数量: 这个值一般来说超过 100,000.

如果 ``n_samples == 10000``, 存储 ``X`` 为 "float32" 型的numpy数组将会需要 10000 x 100000 x 4 bytes = **4GB内存** ，在当前的计算机中非常不好管理的.

幸运的是, **X 数组中大多是 0** ，是因为特定的文档中使用的单词数量远远少于总体的词袋单词个数. 因此我们可以称词袋模型是典型的
**high-dimensional sparse datasets（高维稀疏数据集）**. 我们可以通过只保存那些非0的部分的特征向量在内存中来节省大量内存.

``scipy.sparse`` 矩阵正是能完成上述操作的数据结构,同时 ``scikit-learn`` 有内置的支持这样的数据结构.


使用 ``scikit-learn`` 来对文本进行分词
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

文本的预处理, 分词以及过滤停词包含在可以构建特征字典和将文本转换成特征向量的高级组件中 ::

  >>> from sklearn.feature_extraction.text import CountVectorizer
  >>> count_vect = CountVectorizer()
  >>> X_train_counts = count_vect.fit_transform(twenty_train.data)
  >>> X_train_counts.shape
  (2257, 35788)

:class:`CountVectorizer` 提供了 N-gram 模型以及连续词模型.
一旦拟合, 向量化程序就会构建一个包含特征索引的字典::

  >>> count_vect.vocabulary_.get(u'algorithm')
  4690

在词汇表中一个单词的索引值对应的是该单词在整个训练的文集中出现的频率.

.. note:

  方法 ``count_vect.fit_transform`` 执行了两种操作: 学习词汇并将文档们转换成计数向量.
  当然也可以将步骤分开来做：首先 ``count_vect.fit(twenty_train.data)`` 
  ，其次 ``X_train_counts = count_vect.transform(twenty_train.data)`` 
  ，但是这样做的话每个文件的分词化和向量化就执行了两次.


从出现次数到出现频率
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

出现次数的统计是非常好的开始，但是有个问题：长的文本相对于短的文本有更高的平均单词出现次数，尽管他们可能在描述同一个主题.

使用每个单词出现的次数被除以总共单词出现的次数，这种方法就能避免以上这种潜在的差别：这些新的特征称之为词频（tf）.

基于这词频上的精简提炼的是降低这个文集里出现在很多文档中的单词的比重，因此它会比那些仅出现在一些文档中的单词有更少的信息量.

这种downscaling（降低比重）的方法称为 `tf–idf`_ ，全称为 "Term Frequency times Inverse Document Frequency".

.. _`tf–idf`: https://en.wikipedia.org/wiki/Tf–idf


**tf** 和 **tf–idf** 都可以按照下面的方式计算::

  >>> from sklearn.feature_extraction.text import TfidfTransformer
  >>> tf_transformer = TfidfTransformer(use_idf=False).fit(X_train_counts)
  >>> X_train_tf = tf_transformer.transform(X_train_counts)
  >>> X_train_tf.shape
  (2257, 35788)

在上面的例子中，我们首先使用了 ``fit(..)`` 方法来拟合数据的estimator(估算器)，接下来使用 ``transform(..)`` 方法来把我们的次数矩阵转换成 `tf-idf` 型.
这两步可以结合起来，通过省去多余的处理来更快地得到同样的结果. 那就是使用上节提到过的 ``fit_transform(..)`` 方法，如下::

  >>> tfidf_transformer = TfidfTransformer()
  >>> X_train_tfidf = tfidf_transformer.fit_transform(X_train_counts)
  >>> X_train_tfidf.shape
  (2257, 35788)


训练分类器
---------------------

现在我们有了自己的特征，我们可以训练一个分类器来预测一个帖子所属的类别.
让我们从 :ref:`朴素贝叶斯 <naive_bayes>` 分类器开始吧. 该分类器为该任务上提供了一个好的准线.
``scikit-learn`` 包含了该分类器的若干变种;最适用在该问题上的变种是多项式分类器::

  >>> from sklearn.naive_bayes import MultinomialNB
  >>> clf = MultinomialNB().fit(X_train_tfidf, twenty_train.target)

为了尝试预测新文档所属的类别，我们需要和之前同样的步骤来抽取特征.
区别是，对transformer使用 ``transform`` 而不是 ``fit_transform`` ，因为文档已经与训练集拟合了::

  >>> docs_new = ['God is love', 'OpenGL on the GPU is fast']
  >>> X_new_counts = count_vect.transform(docs_new)
  >>> X_new_tfidf = tfidf_transformer.transform(X_new_counts)

  >>> predicted = clf.predict(X_new_tfidf)

  >>> for doc, category in zip(docs_new, predicted):
  ...     print('%r => %s' % (doc, twenty_train.target_names[category]))
  ...
  'God is love' => soc.religion.christian
  'OpenGL on the GPU is fast' => comp.graphics


构建 Pipeline（管道）
-----------------------

为了使得 向量化 => 转换 => 分类器 过程更加简单,``scikit-learn`` 提供了一个 ``Pipeline`` 类，操作起来像一个复合分类器::

  >>> from sklearn.pipeline import Pipeline
  >>> text_clf = Pipeline([('vect', CountVectorizer()),
  ...                      ('tfidf', TfidfTransformer()),
  ...                      ('clf', MultinomialNB()),
  ... ])

名称 ``vect``, ``tfidf`` 和 ``clf`` （分类器）都是任意的.
我们将会在下面的 grid search（网格搜索）小节中看到它们的用法.
现在我们使用下行命令来训练模型::

  >>> text_clf.fit(twenty_train.data, twenty_train.target)  # doctest: +ELLIPSIS
  Pipeline(...)


评估测试集上的性能
---------------------------------------------

评估模型的精度同样很简单::

  >>> import numpy as np
  >>> twenty_test = fetch_20newsgroups(subset='test',
  ...     categories=categories, shuffle=True, random_state=42)
  >>> docs_test = twenty_test.data
  >>> predicted = text_clf.predict(docs_test)
  >>> np.mean(predicted == twenty_test.target)            # doctest: +ELLIPSIS
  0.834...

如上, 我们模型的精度为 83.4%.
我们使用线性分类模型 :ref:`支持向量机（SVM） <svm>`, 一种公认的最好的文本分类算法（尽管训练速度没有朴素贝叶斯快）.
我们改变分类算法只需要在 pipeline（管道）中添加不同的算法即可::

  >>> from sklearn.linear_model import SGDClassifier
  >>> text_clf = Pipeline([('vect', CountVectorizer()),
  ...                      ('tfidf', TfidfTransformer()),
  ...                      ('clf', SGDClassifier(loss='hinge', penalty='l2',
  ...                                            alpha=1e-3, random_state=42,
  ...                                            max_iter=5, tol=None)),
  ... ])
  >>> text_clf.fit(twenty_train.data, twenty_train.target)  # doctest: +ELLIPSIS
  Pipeline(...)
  >>> predicted = text_clf.predict(docs_test)
  >>> np.mean(predicted == twenty_test.target)            # doctest: +ELLIPSIS
  0.912...

``scikit-learn`` 同样提供了更加细节化的模型评估工具::

  >>> from sklearn import metrics
  >>> print(metrics.classification_report(twenty_test.target, predicted,
  ...     target_names=twenty_test.target_names))
  ...                                         # doctest: +NORMALIZE_WHITESPACE
                          precision    recall  f1-score   support
  <BLANKLINE>
             alt.atheism       0.95      0.81      0.87       319
           comp.graphics       0.88      0.97      0.92       389
                 sci.med       0.94      0.90      0.92       396
  soc.religion.christian       0.90      0.95      0.93       398
  <BLANKLINE>
             avg / total       0.92      0.91      0.91      1502
  <BLANKLINE>

  >>> metrics.confusion_matrix(twenty_test.target, predicted)
  array([[258,  11,  15,  35],
         [  4, 379,   3,   3],
         [  5,  33, 355,   3],
         [  5,  10,   4, 379]])


从混淆矩阵中可以看出，新闻所属类别在无神论以及基督教两者之间是非常令人困惑的，而与计算机图形学差别是特别大的.

.. note:

  SGD 表示 Stochastic Gradient Descent（随机梯度下降算法）.
  这是一个非常简单的优化算法，尤其是在大规模数据集下显得特别有效.

  通过设置 ``loss="hinge"`` 和 ``penalty="l2"`` 我们可以通过微调分类算法的参数来设置支持向量机的损失函数.

  当然我们也可以使用 ``sklearn.svm.LinearSVC`` （线性支持向量机分类器），提供了一种基于 C 语言编写的 liblinear 库的损失函数优化器.

.. _liblinear: http://www.csie.ntu.edu.tw/~cjlin/liblinear/


使用网格搜索进行参数调优
----------------------------------

我们已经接触了类似于 ``TfidfTransformer`` 中 ``use_idf`` 这样的参数 ，分类器有多种这样的参数;
比如, ``MultinomialNB`` 包含了平滑参数 ``alpha`` 以及 ``SGDClassifier`` 有惩罚参数 ``alpha`` 和设置损失以及惩罚因子（更多信息请使用 python 的 ``help`` 文档）.

而不是调整 chain（链条）的各种组件的参数, 通过构建巨大的网格搜索来寻找最佳参数也是可以的.
我们可以对线性支持向量机使用每个单词或者使用 n-gram, 是否使用 idf, 以及设置从 0.01 到 0.001 的惩罚参数::

  >>> from sklearn.model_selection import GridSearchCV
  >>> parameters = {'vect__ngram_range': [(1, 1), (1, 2)],
  ...               'tfidf__use_idf': (True, False),
  ...               'clf__alpha': (1e-2, 1e-3),
  ... }

很明显的可以发现, 如此的搜索是非常耗时的.
如果我们有多个 cpu 核心可以使用, 通过设置 ``n_jobs`` 参数能够进行并行搜索.
如果我们将该参数设置为 ``-1``, 该方法会使用机器的所有 cpu 核心::

  >>> gs_clf = GridSearchCV(text_clf, parameters, n_jobs=-1)

网格搜索在 ``scikit-learn`` 中是非常常见的.
让我们来选择一部分训练集来加速训练::

  >>> gs_clf = gs_clf.fit(twenty_train.data[:400], twenty_train.target[:400])

在 ``GridSearchCV`` 中使用 ``fit``，因此我们也能够使 ``predict``::

  >>> twenty_train.target_names[gs_clf.predict(['God is love'])[0]]
  'soc.religion.christian'

对象 ``best_score_`` 和 ``best_params_`` 存放了最佳的平均分数以及其所对应的参数::

  >>> gs_clf.best_score_                                  # doctest: +ELLIPSIS
  0.900...
  >>> for param_name in sorted(parameters.keys()):
  ...     print("%s: %r" % (param_name, gs_clf.best_params_[param_name]))
  ...
  clf__alpha: 0.001
  tfidf__use_idf: True
  vect__ngram_range: (1, 1)

更多的详细信息可以在 ``gs_clf.cv_results_`` 中发现.

``cv_results_`` 参数也能够被导入进 ``DataFrame``，供后期使用.

.. note:

  ``GridSearchCV`` 对象也保存了最好的分类器保存在 ``best_estimator_`` 属性中.
  在这个例子中, 训练一个仅仅有 400 个文档的子数据集没有什么效果的.


练习
~~~~~~~~~

为了做这个练习, 请拷贝 'skeletons' 文件夹到新的文件夹，并将其命名为 'workspace'::

  % cp -r skeletons workspace

这时候可以任意更改练习的代码而不会破坏原始的代码结构.

然后启动 ipython 交互环境，并键入以下代码::

  [1] %run workspace/exercise_XX_script.py arg1 arg2 arg3

如果出现错误, 请使用 ``%debug`` 来启动 ipdb 调试环境.

迭代更改答案直到练习完成.

**在每个练习中, skeleton 文件包含了做练习使用的一切数据及代码.**


练习 1：语言识别
-----------------------------------

- 请使用自定义的预处理器和 ``CharNGramAnalyzer``，并且使用维基百科中的文章作为训练集，来编写一个文本分类的 pipeline（管道）.

- 评估某些测试套件的性能.

ipython command line::

  %run workspace/exercise_01_language_train_model.py data/languages/paragraphs/


练习 2：电影评论上的情绪分析
-----------------------------------------------

- 编写一个文本分类 pipeline（管道）来将电影评论分类为正面的还是负面的.

- 使用网格搜索来找到最好的参数集.

- 使用测试集来测试分类性能.

ipython 命令行::

  %run workspace/exercise_02_sentiment.py data/movie_reviews/txt_sentoken/


练习 3：CLI 文本分类实用程序
-------------------------------------------

使用刚刚的练习的结果以及标准库 ``cPickle`` , 编写一个命令行工具来检测输入的英文的文本是正面信息还是负面的.

如果实用程序能够给出其预测的置信水平，就可以得到奖励.


快速链接
------------------

当你完成这个章节时，以下是几个建议:


* 尝试使用 :class:`CountVectorizer` 类下的 ``analyzer`` 以及 ``token normalisation`` .

* 如果你没有标签, 使用 :ref:`聚类 <sphx_glr_auto_examples_text_document_clustering.py>` 来解决你的问题.

* 如果对每一篇文章有多个标签，请参考 :ref:`多类别和多标签部分 <multiclass>` _.

* 使用 :ref:`Truncated SVD <LSA>` 解决 `潜在语义分析 <https://en.wikipedia.org/wiki/Latent_semantic_analysis>`_.

* 使用 :ref:`Out-of-core Classification <sphx_glr_auto_examples_applications_plot_out_of_core_classification.py>` 来在没有全部读入数据来进行机器学习.

* 使用 :ref:`Hashing Vectorizer <hashing_vectorizer>` 另一种方案来节省内存 :class:`CountVectorizer` .
