# 贡献指南

> 请您勇敢地去翻译和改进翻译。虽然我们追求卓越，但我们并不要求您做到十全十美，因此请不要担心因为翻译上犯错——在大部分情况下，我们的服务器已经记录所有的翻译，因此您不必担心会因为您的失误遭到无法挽回的破坏。（改编自维基百科）

可能有用的链接：

+ [英文官网](https://scikit-learn.org)
+ [中文翻译](https://sklearn.apachecn.org)

## 章节列表

*   [安装 scikit-learn](docs/master/62.md)
*   用户指南
    *   [1. 监督学习](docs/master/1.md)
        * [1.1. 广义线性模型](docs/master/2.md)
        * [1.2. 线性和二次判别分析](docs/master/3.md)
        * [1.3. 内核岭回归](docs/master/4.md)
        * [1.4. 支持向量机](docs/master/5.md)
        * [1.5. 随机梯度下降](docs/master/6.md)
        * [1.6. 最近邻](docs/master/7.md)
        * [1.7. 高斯过程](docs/master/8.md)
        * [1.8. 交叉分解](docs/master/9.md)
        * [1.9. 朴素贝叶斯](docs/master/10.md)
        * [1.10. 决策树](docs/master/11.md)
        * [1.11. 集成方法](docs/master/12.md)
        * [1.12. 多类和多标签算法](docs/master/13.md)
        * [1.13. 特征选择](docs/master/14.md)
        * [1.14. 半监督学习](docs/master/15.md)
        * [1.15. 等式回归](docs/master/16.md)
        * [1.16. 概率校准](docs/master/17.md)
        * [1.17. 神经网络模型（有监督）](docs/master/18.md)
    *   [2. 无监督学习](docs/master/19.md)
        * [2.1. 高斯混合模型](docs/master/20.md)
        * [2.2. 流形学习](docs/master/21.md)
        * [2.3. 聚类](docs/master/22.md)
        * [2.4. 双聚类](docs/master/23.md)
        * [2.5. 分解成分中的信号（矩阵分解问题）](docs/master/24.md)
        * [2.6. 协方差估计](docs/master/25.md)
        * [2.7. 新奇和异常值检测](docs/master/26.md)
        * [2.8. 密度估计](docs/master/27.md)
        * [2.9. 神经网络模型（无监督）](docs/master/28.md)
    * [3. 模型选择和评估](docs/master/29.md)
        * [3.1. 交叉验证：评估估算器的表现](docs/master/30.md)
        * [3.2. 调整估计器的超参数](docs/master/31.md)
        * [3.3. 模型评估: 量化预测的质量](docs/master/32.md)
        * [3.4. 模型持久化](docs/master/33.md)
        * [3.5. 验证曲线: 绘制分数以评估模型](docs/master/34.md)
    * [4.  检验](docs/master/35.md)
        * [4.1. 部分依赖图](docs/master/36.md)
    * [5. 数据集转换](docs/master/37.md)
        * [5.1. Pipeline（管道）和 FeatureUnion（特征联合）: 合并的评估器](docs/master/38.md)
        * [5.2. 特征提取](docs/master/39.md)
        * [5.3 预处理数据](docs/master/40.md)
        * [5.4 缺失值插补](docs/master/41.md)
        * [5.5. 无监督降维](docs/master/42.md)
        * [5.6. 随机投影](docs/master/43.md)
        * [5.7. 内核近似](docs/master/44.md)
        * [5.8. 成对的矩阵, 类别和核函数](docs/master/45.md)
        * [5.9. 预测目标 (`y`) 的转换](docs/master/46.md)
    * [6. 数据集加载工具](docs/master/47.md)
        * [6.1. 通用数据集 API](docs/master/47.md)
        * [6.2. 玩具数据集](docs/master/47.md)
        * [6.3 真实世界中的数据集](docs/master/47.md)
        * [6.4. 样本生成器](docs/master/47.md)
        * [6.5. 加载其他数据集](docs/master/47.md)
    * [7. 使用scikit-learn计算](docs/master/48.md)
        * [7.1. 大规模计算的策略: 更大量的数据](docs/master/48.md)
        * [7.2. 计算性能](docs/master/48.md)
        * [7.3. 并行性、资源管理和配置](docs/master/48.md)
*   [教程](docs/master/50.md)
    *   [使用 scikit-learn 介绍机器学习](docs/master/51.md)
    *   [关于科学数据处理的统计学习教程](docs/master/52.md)
        *   [机器学习: scikit-learn 中的设置以及预估对象](docs/master/53.md)
        *   [监督学习：从高维观察预测输出变量](docs/master/54.md)
        *   [模型选择：选择估计量及其参数](docs/master/55.md)
        *   [无监督学习: 寻求数据表示](docs/master/56.md)
        *   [把它们放在一起](docs/master/57.md)
        *   [寻求帮助](docs/master/58.md)
    *   [处理文本数据](docs/master/59.md)
    *   [选择正确的评估器(estimator.md)](docs/master/60.md)
    *   [外部资源，视频和谈话](docs/master/61.md)
*   [API 参考](https://scikit-learn.org/stable/modules/classes.html)
*   [常见问题](docs/master/63.md)
*   [时光轴](docs/master/64.md)

## 流程

### 一、认领

首先查看[整体进度](https://github.com/apachecn/sklearn-doc-zh/issues/352)，确认没有人认领了你想认领的章节；当然如果你想完善已校对的章节，我们也十分欢迎。

然后回复 ISSUE，注明“章节 + QQ 号”（一定要留 QQ）。

### 二、校对

#### 完善方向

可以完善的方向包括但不限于：

1.  中英文符号（Chinese prior）；
2.  笔误及错误语法；
3.  术语使用；
4.  语言润色；
5.  文档格式；
6.  如果觉得现有翻译的某些部分不好，重新翻译也是可以的。

#### 关于数学公式

尽管用MathJax等工具插入数学公式是一个好的 manner，但是我们目前并不把它列为 high-priority 的提升方向。我们未来会做的！但是针对于这个问题如果你有好的想法并乐意PR，未来我们会针对于这个新特性做一些改进。

如果你发现公式过期或者错误，请务必按照这种格式进行更新："! + [latex 公式] + (图片地址) "，这样可以保证我们的开发比较高效。此外，不要忘记将新的图片放到 img 文件夹中一并 PR。如果你找不到好的latex公式图片下载地址，可以使用[这个工具](http://latex.codecogs.com/eqneditor/editor.php)。

#### 管理者校对

管理员应当是组织内活跃的参与者，因此可能会从事很多校对工作。我们建议管理员自己不要 merge 自己对于文档修改或者增加新特性的PR，这样其他管理员可以 review 并 double check，提升文档质量。

### 三、提交

**提交的时候不要改动文件名称，即使它跟章节标题不一样也不要改，因为文件名和原文的链接是对应的！！！**

+   `fork` Github 项目并建立你的分支 `branch`（我们强烈建议这样做）；
+   将译文放在 `docs/master` 文件夹下；
+   `commit` 和 `push` 你的修改；
+   `pull request`。

如果你还不熟练这个流程，请参阅 [Github 入门指南](https://github.com/apachecn/kaggle/blob/master/docs/GitHub)。
