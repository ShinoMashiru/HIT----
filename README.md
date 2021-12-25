# 大型网络超图研究

### 目录
**[简介](#introduction)** <br>
**[超图算法](#hypergraph-algorithms)** <br>
**[超图配置](#hypergraph-configuration)** <br>
**[初始结果](#initial-results)** <br>
**[安装](#installation)** <br>
**[有用的运行命令](#useful-run-commands)** <br>
**[结论](#conclusion)** <br>
**[参考](#reference)** <br>

## 介绍

在考虑组交互时，超图是一种有用的数据表示。考虑一个作者网络，其中有一组作者——即作者 A、作者 B 和作者 C，他们共同撰写了一份出版物。使用传统的图边来表示这些信息，其中每条边连接两个作者以显示共同作者关系带来两个问题。首先，它引入了误导性信息，我们无法区分三位作者的单一出版物的情况；其中三位作者合着了三篇出版物（即作者A和作者B合着的出版物一；作者B和作者C合着的出版物二；作者A和作者C合着的出版物三）。使用传统图表示表示超图的第二个含义是图存储中数据的膨胀。例如，在我们之前的合着网络中，我们将需要添加 N*(N-1) 条边来表示 N 个作者之间对于单个出版物的合着。通过学习如何将数据编码到这些结构中以及如何操作它们以提取有意义的数据，我们可能能够获得有关以前不可能实现的数据的见解。

本研究的目的之一是了解超图并找到它们在大型网络中的适应性。我们已经探索了超图的几种表示方式，并了解了每种表示方式的优缺点。在追求这个方向的过程中，对在这个方向上工作的现有库 [1]、[2] 进行了几次探索性搜索。这些库允许使用不同的编码和超图的实现来对不同的算法进行基准测试。但是这些库存在缺陷——尤其是当数据源很大且不固定时，如推文、邮件、社交网络交互或财务数据。
使用 Apache Spark 将现有的并行超图算法实现移植到分布式环境中。我们构建此 Python 模块以使用 Apache Spark 在分布式设置中探索超图 PageRank 实现。

## 超图算法
### 网页排名
Pagerank 是一种常用的图分析算法。它用于查找网络中顶点的相对重要性。在很多应用中，pagerank 起着非常重要的作用，例如在推荐系统、链接预测、搜索引擎等中。我们可以用两种不同的方式来考虑 pagerank 在超图中的含义。首先，顶点在网络中的重要性基于它们的组参与。例如，在社交网络上下文中，我们可以根据组成员身份来衡量用户的重要性，例如，拥有一小群用户的组的管理员可能对整个网络有更大的影响。其次，超边的重要性基于它所链接的顶点。例如，

## 超图配置

表示超图的一种方法是在每个超边的所有顶点对之间创建一个团，并将结果存储为正则图。正如我们之前提到的，这会导致一些问题，例如信息丢失。此外，空间开销明显高于原始超图的空间开销。另一种方法是使用二部图表示，其中一个分区中的参与顶点和另一个分区中的超边。我们使用这种二部图表示保留超图信息，其中超边连接到参与顶点，如下图所示。

<p align="center">
  <img src="https://github.com/biqar/hypergraph-study/blob/main/resources/example_hypergraph.png" />
  <p align="center">图 1 - 表示组 {0,1,2}、{1,2,3} 和 {0,3} 的示例超图及其来自 [3]<p 的二部表示对齐=“居中”>
</p>

## 初步结果

Apache Spark 2.4.7 是通过使用 Databricks GraphFrames API 0.8.1 在 Python 中实现的，Databricks GraphFrames API 0.8.1 是一个图形处理框架，在其上实现了超图算法。

<表>
  <tr>
    <td>
       <人物>
        <img align="middle" src="https://github.com/biqar/hypergraph-study/blob/main/resources/evaluation_1.png" alt="evaluation_1"/>
        </图>
    </td>
    <td>
      <人物>
        <img align="middle" src="https://github.com/biqar/hypergraph-study/blob/main/resources/evaluation_2.png" alt="evaluation_2"/>
      </图>
    </td>
  </tr>
  <tr>
    <td>
       <人物>
        <img align="middle" src="https://github.com/biqar/hypergraph-study/blob/main/resources/evaluation_3.png" alt="evaluation_3"/>
        </图>
    </td>
    <td>
      <人物>
        <img align="middle" src="https://github.com/biqar/hypergraph-study/blob/main/resources/evaluation_4.png" alt="evaluation_4"/>
      </图>
    </td>
  </tr>
  <tr>
    <td colspan="2" align="middle">图 2 - PageRank 的实验结果。</td>
  </tr>
</table>

在图 2(i) 中，我们比较了 PageRank 算法的分布式分区和算法执行时间。对于所有大型数据集，分区成本明显低于算法执行时间。在图 2(ii) 中，我们绘制了 PageRank 算法对不同数据集的整体性能。在这里，orkut-5000 图形与其他图形相比需要更长的运行时间。原因是，orkut-5000 具有更大的超边（即超边中参与节点的数量）。例如，与 dbpl-5000 相比，orkut-5000 的超边要大 12 倍。另一方面，dbpl-5000 的运行速度仅比 orkut-5000 快 6 倍。这实际上意味着在分布式环境中运行大型超图的用处。在图 2(iii) 和图 2(iv) 中，我们比较了两种超图表示（例如 clique 和 bipartite 表示）分别用于 email-enron 和 Friendster-5000。我们无法在 orkut-5000 和 dbpl-5000 的超图的集合实现上运行 PageRank，因为这些图非常大，无法将它们放入我们的测试平台设置中。这进一步表明了我们研究的重要性。图 2(iv) 表明，与执行时间相比，对于较大的超图，二分表示中的分区时间显着减少。这在意料之中，因为在集团表示中，我们必须放置很多边，因此以指数方式增加分区时间。因为这些图非常大，无法将它们放入我们的测试台设置中。这进一步表明了我们研究的重要性。图 2(iv) 表明，与执行时间相比，对于较大的超图，二分表示中的分区时间显着减少。这在意料之中，因为在集团表示中，我们必须放置很多边，因此以指数方式增加分区时间。因为这些图非常大，无法将它们放入我们的测试台设置中。这进一步表明了我们研究的重要性。图 2(iv) 表明，与执行时间相比，对于较大的超图，二分表示中的分区时间显着减少。这在意料之中，我们必须放置很多边，因此以指数方式增加分区时间。


## 安装
安装需要以下库
1) Java 7+
2) Apache Spark（2.4 版中测试的代码）
    *请使用此链接安装spark：https://spark.apache.org/docs/latest/
3）图形框架（在0.8.1版本中测试）
    *请使用它来安装graphframes：https://graphframes.github.io/graphframes/docs/_site/index.html

## 有用的运行命令
使用spark提交，如：
``
> spark-submit --packages graphframes:graphframes:0.8.1-spark2.4-s_2.11 <python-code.py>
``

要运行二分页面排名，请执行 
``
> spark-submit --packages graphframes:graphframes:0.8.1-spark2.4-s_2.11 bipartite_page_rank.py
``

运行pagerank
``
> spark-submit --packages graphframes:graphframes:0.8.1-spark2.4-s_2.11 clique_page_rank.py
``

## 结论
总之，基于当前用于超图实验和实现的资源，构建一个允许在分布式环境中为大型网络创建和测试超图的库，以及一系列超图算法，将是一项值得的努力。我们计划检查我们当前实现的性能，看看我们如何改进它们。进一步，我们将探索超图分区研究对超图算法分布式计算的影响。

## 参考
1. Pacific Northwest National Laboratory, Python package for hypergraph analysis and visualization., (2020), GitHub repository, https://github.com/pnnl/HyperNetX
2. Leland McInnes, A library for hypergraphs and hypergraph algorithms., (2015), GitHub repository, https://github.com/lmcinnes/hypergraph
3. Julian Shun. 2020. Practical parallel hypergraph algorithms. In Proceedings of the 25th ACM SIGPLAN Symposium on Principles and Practice of Parallel Programming (PPoPP '20). Association for Computing Machinery, New York, NY, USA, 232–249. DOI:https://doi.org/10.1145/3332466.3374527
4. Y. Gu, et al.,"Distributed Hypergraph Processing Using Intersection Graphs" in IEEE Transactions on Knowledge & Data Engineering, vol. , no. 01, pp. 1-1, 5555. doi: 10.1109/TKDE.2020.3022014
