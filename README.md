# 面向电信行业存量用户的智能套餐个性化匹配模型源码
一．项目概述及计划
1.项目概述
此题利用已有的用户属性(如个人基本信息、用户画像信息等)、终端属性(如终端品牌等)、业务属性、消费习惯及偏好匹配用户最合适的套餐，对用户进行推送，完成后续个性化服务，是一个多分类任务。 因此我们在具体实现的过程中需要向机器学习中处理分类问题的模型上考虑，包括向量机、决策树等。
2.项目计划
首先对赛题进行标签化的分析：问题分析、数据分析预处理、分类预测、回归分析，根据这些信息数据建立特征工程，大致预测需要使用的模型与相关算法。使用 SVM 进行测试，根据测试情况考虑改用其它相关算法。比如用决策树、随机森林和 LightGBM 等对模型进行优化，最终获得结果。在实现不同模型的过程中进行深入的探究，项目课题包括：对特征选择的思考，相关系数在这次的任务中是否合适，按照不同service_type 的预测结果的差距等内容。

二．问题描述
1.赛题简介
电信产业作为国家基础产业之一，覆盖广、用户多，在支撑国家建设和发展方面尤为重要。随着互联网技术的快速发展和普及，用户消耗的流量也成井喷态势，近年来，电信运营商推出大量的电信套餐用以满足用户的差异化需求，面对种类繁多的套餐，如何选择最合适的一款对于运营商和用户来说都至关重要，尤其是在电信市场增速放缓，存量用户争夺愈发激烈的大背景下。针对电信套餐的个性化推荐问题，通过数据挖掘技术构建了基于用户消费行为的电信套餐个性化推荐模型，根据用户业务行为画像结果，分析出用户消费习惯及偏好，匹配用户最合适的套餐，提升用户感知，带动用户需求，从而达到用户价值提升的目标。
套餐的个性化推荐，能够在信息过载的环境中帮助用户发现合适套餐，也能将合适套餐信息推送给用户。解决的问题有两个：信息过载问题和用户无目的搜索问题。各种套餐满足了用户有明确目的时的主动查找需求，而个性化推荐能够在用户没有明确目的的时候帮助他们发现感兴趣的新内容。
2.赛题任务
此题利用已有的用户属性(如个人基本信息、用户画像信息等)、终端属性(如终端品牌等)、业务属性、消费习惯及偏好匹配用户最合适的套餐，对用户进行推送，完成后续个性化服务。

3.赛题评分方式
      
4.赛题训练数据（trainData-V1.json）
    
三．模型建模
在解答赛题时我先后选择了四种模型进行建模，分别是SVM、决策树、随机森林和LightGBM。通过实验结果对比了四种模型的优缺点，下面分别介绍一下这四种模型的具体建模过程。
1.SVM模型
（1）模型介绍
支持向量机（SVM）是一类按监督学习方式对数据进行二元分类的广义线性分类器，其决策边界是对学习样本求解的最大边距超平面。SVM使用铰链损失函数计算经验风险，并在求解系统中加入了正则化项以优化结构风险，是一个具有稀疏性和稳健性的分类器 。SVM可以通过核方法进行非线性分类，是常见的核学习方法之一 。
（2）模型训练
根据网上博文的代码进行模仿实现，替换其中的一部分输入，将我们的测试数据进行比对，主要按照 total_fee 系列数据为主要的参考数据进行学习分析。最终测试预测的准确率约为 63%。经过大量的调参，修改函数之后还是没有取得很好的效果，采用前 60 万行数据学习预测后 10 万行的准确率没有超过 68%，因此判断出 SVM 并不是一个很适合这个问题的解决方法。这个是由其原理决定的，它适合的是线性程度较高的回归学习，但是这次任务并不适合简单的线性回归，原因在最后的反思中有提到，这次的套餐类型、用户画像并不是一个稳定的数值，而是离散型关联程度较低的字符变量，因此相关系数等并不能作为一个很好的解决方法。我也因将关注点转移到了决策树上面去。
2.决策树模型
（1）模型介绍
决策树是一种基本的分类与回归方法，它可以认为是定义在特征空间与类空间的条件概率分布。决策树思想，实际上就是寻找最纯净的划分方法。决策树模型不同于线性模型，线性模型是所有特征赋予不同的权值相加得到结果，而决策树则是单个特征进行处理，每一步寻找一个最优特征进行划分。决策树与逻辑回归的不同之处也在于此，逻辑回归是根据所有特征求出概率，然后与某一阈值进行比较从而分类，而决策树每一步是通过最优特征进行划分，直到叶节点。决策树的学习过程主要包括3个步骤：特征选择、决策树的生成和决策树的剪枝。
（2）模型训练
首先，选择 sklearn 库中的 tree.DecisionTreeClassifier 进行决策树上的尝试，在训练集上进行 train_test_split 并进行训练与预测，设置深度为 8，达到了 86%的预测准确率，f1_score 达到 0.832。可以看出，决策树模型在这个问题上表现很好，在一开始就达到较好的成果。逐渐提高深度，可以看出 f1_score 呈现先上升后下降的趋势，并且在训练数据和测试数据上出现较大差异，呈现过拟合的现象。如下图所示。

  
      
        
为了增强优度，采用 GridSearchCV 进行模型的重新建立。这里不再采用 
train_test_split，而是选用train_all.csv作为已知数据进行训练。结果如下：
3.随机森林模型
（1）模型介绍
随机森林是由很多决策树构成的，不同决策树之间没有关联。当我们进行分类任务时，新的输入样本进入，就让森林中的每一棵决策树分别进行判断和分类，每个决策树会得到一个自己的分类结果，决策树的分类结果中哪一个分类最多，那么随机森林就会把这个结果当做最终的结果。
大致过程如下：
（a）从样本集中有放回随机采样选出 n 个样本；
(b）从所有特征中随机选择 k 个特征，对选出的样本利用这些特征建立决策树（一般是CART，也可是别的或混合）；
（c）重复以上两步 m 次，即生成 m 棵决策树，形成随机森林；
（d）对于新数据，经过每棵树决策，最后投票确认分到哪一类。
（2）模型训练
选择 sklearn 库中的 RandomForestClassifier 进行随机森林的尝试，在训练集上进行 train_test_split 并进行训练与预测。数将前 600000 行作为训练数据训练随机森林模型，600000 行之后作为测试数据进行测试，得到的结果（准确率）为 92.6827235602967 %。 若按照 service_type 分开训练、检测，那么 service_type1 的准确率为 99.2409 %，service_type4 的准确率为 86.7679%。可以看出，随机森林模型在这个问题上的表现优总体优于决策树，在一开始就达到很好的成果。分析原因，应该是每棵树都选择部分样本及部分特征，一定程度避免了过拟合现象；同时，每棵树随机选择样本并随机选择特征，使得具有很好的抗噪能力，性能稳定。但很快发现了新问题，发现效果开始变差。为了分析效果变差的原因，随后对 service_type4 这一部分的数据进行调参训练。
设置参数：对于最小叶子节点的参数取值，从 1 到 500 每隔 3 取 1 个；对于决策树个数的参数取值，从 1 到 1000 每隔 5 取一个。由于随机森林的模型训练和预测都很慢，再加上机器限制，所以这里实际上只跑完了 60 多组，用了大约 20h。模型遍历所有参数组合，并分别输出当前组合的准确率和 f1_score。在遍历结束后后输出f1_score 最高的参数组合。训练过程中，可以看到，不同参数组合的得分并没有过大的波动。所以之前“效果下降的原因是过拟合”的猜测不成立。猜想service_type4 的预测效果不尽如人意的原因可能与该种类本身的特性有关。在前文中提到的通过遍历不同的参数组合来观察随机森林模型预测结果的变化，一定程度上排除了参数的特殊性影响模型效果的可能性。但是随机森林模型的一个缺点就是参数较复杂，模型训练和预测都比较慢。所以在有限的时间内，能够遍历的参数组合是很少的。所以还是不能排除其余参数和现处理参数的特殊性对实验造成影响的可能。
4.LightGBM模型
（1）模型介绍
LightGBM 是微软开发的一款快速、分布式、高性能的基于决策树的梯度 Boosting 框架。其拥有基于 Histogram 的决策树算法，直方图做差加速，带深度限制的 Leaf-wise 的叶子生长策略，直接支持类别特征，基于直方图的稀疏特征优化，多线程优化的改进。主要有更快的训练效率、低内存使用、更好的准确率、支持并行学习、可处理大规模数据等优势。
（2）模型训练
首先使用sklearn包对测试集和训练集进行划分，使用lightgbm 库中的LGBMClassifier分类器搭建 LightGBM模型。
构建参数词典：

评价函数采用 F1-macro 宏平均： 
  
采用 5折交叉验证的方式进行训练，即 4 个训练集，1 个测试集： 


该模型将开始训练，直到验证得分不再提高，最终选择的一组参数如下：

预测准确率：92.74208400840244%
F1-macro score 得分为： 0.72

四．实验分析
4.1数据分析
1.数据预处理与可视化
 首先对训练数据进行分析。经查，发现没有重复的数据。对于\\N 类型的数值缺失数据的，查找发现有 4 个数据，因为数据量足够多、有 74多万条，所以剔除掉这几处\\N类型的数值缺失；为了方便对套餐进行分类，将 11 个类别的套餐分别标记为1-11 号；为方便后续处理，在查阅资料之后选用 python pandas 库函数，将数据读取到 DataFrame 中。
      
为了大致明确各个标签下数据的分布情况，对数据进行可视化。用seaborn 绘制 current_service（标签）与其它特征的箱线图，查看数值分散情况、平均值和是否有过于异常的值。

        
使用seaborn绘制current_service（标签）与其余特征的关系图，查看current_service在其它特征上的分布情况和其余特征自身的分布情况。

        
计算 current_service与其它特征的相关系数（相关系数能在某种程度上表明二者之间联系的强弱）。绘制了相关系数热力图，让相关性更加直观。
          

2.数据分析
 由于对电信领域知识有限，采用的方法是假定所有的特征都可能会帮助分类，再对有足够把握去除的特征进行剔除，类似假设检验的思想。上面得到的数据分布可视化作图可以帮助判断。此外由于数据已经变成数值，我们采用特征和套餐类别之间的相关性来辅助判断。相关性可以用相关系数来衡量，进一步我们对相关系数矩阵制作热力图，以方便对特征进行选择。判断上，可以先查看特征与最终套餐类别的相关程度，相关程度高的优先保留；也可以查看特征之间的相关程度，相关程度高的可以考虑进行选择性保留或者进行组合。

相关系数矩阵热力图：



4.2 实验环境
Window10PC
Python3.8
4.3 实验结果

4.4 实验对比（竞赛排名）
通过实验可以看出，SVM 作为一个能够较好解决线性分类问题的成熟模型，一开始作为一个测试性质的模型实现，效果不佳；而决策树更接近人类认知规律，容易理解和实现，鲁棒性较好，对于这种复杂分类的任务更方便，但容易过拟合，对复杂数据和缺失值不敏感；随机森林数据相关性较高，增强泛化能力，以此一定程度上解决过拟合的问题。LightGBM，在包含 XGBoost 优点的同时进一步提升训练速度，优化训练结果，更适合硬件设施较弱的情况。从结果上来看，SVM 的 f1_score 最低，仅有 0.5026，其余三个的得分依次增长，效果不断提升。虽然几个模型之间的提升效果并不明显，但是整体呈现一个上升的趋势，能够证明基于决策树的梯度 Boosting 框架LightGBM 是更加优秀的分类回归问题的解决模型。

五．项目总结
通过本次项目，我了解了针对一个具体问题如何应用机器学习进行分析求解的一般流程，学习了时序分析分析法针对周期性问题分析的一些方法；对于数据的可视化可以让我们对数据有更深入的理解，挖掘其中的潜在联系，发现重要的特征，因此如何将数据可视化的做好需要进一步的学习。对于模型的选择与优化要学会积极查阅资料，总结前辈解决问题的优秀方法与思路，同时注重理论的研究才能对模型的优化有高效的思路。本次项目最大的困难在于学习使用 python 相关函数与库，以及训练参数时对计算机硬件的要求，其中随机森林和XGBoost对硬件要求较高，运行时间长。另外，可能由于缺少经验，使得作为结果很大影响因子的数据预处理部分，做得稍显不足，包括套餐分类随意和数据分析不足，仅凭肉眼和直觉分辨箱线图和相关系数图等来简单处理数据。对于本道赛题模型表现不是很好，可能是对特征的提取不够充分，也可能模型选择的不够好，还有优化的可能，仍有很大的进步空间，在之后会持续改进。
