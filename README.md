# HIT----
哈工大2021年大数据计算基础大作业
大型网络的超图研究
目录
介绍
Hypergraph 算法
Hypergraph 配置
初始结果
安装
有用的运行命令
结论
参考

介绍
在考虑组交互时，超图是一种有用的数据表示。考虑一个作者网络，其中有一组作者——即作者 A、作者 B 和作者 C，他们共同撰写了一份出版物。使用传统的图边来表示这些信息，其中每条边连接两个作者以显示共同作者关系带来两个问题。首先，它引入了误导性信息，我们无法区分三位作者的单一出版物的情况；其中三位作者合着了三篇出版物（即作者A和作者B合着的出版物一；作者B和作者C合着的出版物二；作者A和作者C合着的出版物三）。使用传统图表示表示超图的第二个含义是图存储中数据的膨胀。例如，在我们之前的合着网络中，我们将需要添加 N*(N-1) 条边来表示 N 个作者之间对于单个出版物的合着。通过学习如何将数据编码到这些结构中以及如何操作它们以提取有意义的数据，我们可能能够获得有关以前不可能实现的数据的见解。

本研究的目的之一是了解超图并找到它们在大型网络中的适应性。我们已经探索了超图的几种表示方式，并了解了每种表示方式的优缺点。在追求这个方向的过程中，我们对在这个方向上工作的现有库 [1]、[2] 进行了几次探索性搜索。这些库允许使用不同的编码和超图的实现来对不同的算法进行基准测试。但是这些库存在缺陷——尤其是当数据源很大且不固定时，如推文、邮件、社交网络交互或财务数据。

在救援中，研究人员通过提出并行超图算法 [3] 进行了多次尝试，该算法在单台机器上的大型数据集上运行良好。越来越需要在分布式设置中处理超图算法，以应对超出内存限制的数据挑战。在我们的工作中，我们使用 Apache Spark 将现有的并行超图算法实现移植到分布式环境中。我们构建此 Python 模块以使用 Apache Spark 在分布式设置中探索超图 PageRank 实现。

超图算法
网页排名
Pagerank 是一种常用的图分析算法。它用于查找网络中顶点的相对重要性。在很多应用中，pagerank 起着非常重要的作用，例如在推荐系统、链接预测、搜索引擎等中。我们可以用两种不同的方式来考虑 pagerank 在超图中的含义。首先，顶点在网络中的重要性基于它们的组参与。例如，在社交网络上下文中，我们可以根据组成员身份来衡量用户的重要性，例如，拥有一小群用户的组的管理员可能对整个网络有更大的影响。其次，超边的重要性基于它所链接的顶点。例如，

超图配置
表示超图的一种方法是在每个超边的所有顶点对之间创建一个团，并将结果存储为正则图。正如我们之前提到的，这会导致一些问题，例如信息丢失。此外，空间开销明显高于原始超图的空间开销。另一种方法是使用二部图表示，其中一个分区中的参与顶点和另一个分区中的超边。我们使用这种二部图表示保留超图信息，其中超边连接到参与顶点，如下图所示。
