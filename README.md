# 基于SEMMA的网络安全事件可视探索
本项目获得中国电子协会举办的长风杯比赛西南赛区二等奖，并发表于中文核心期刊《浙江大学学报》

## 1：项目概述
![系统概览](https://github.com/zzhongying/SEMMA/blob/db422c21264349c2cd31541b321bb856ef9b796b/images/%E5%9B%BE%E7%89%874%E4%BF%AE%E6%94%B9%E5%89%8D.png)
- - - - -  #####  A.Timeline 图；B.嵌套饼图；C.流量折线图；D.PBNLD 图；E.流量双折线图；F.Sankey 图；G.热力图；
- - - - - - - - - - - - - - - - - - - - - - - - - - - ##### H.密码气泡图；I.词云图；J.主机行为聚类图。
- 网络安全可视化能够直观地提取网络安全特征、全方位感知网络安全态势，但如何对网络安全的整体分析流程进行宏观把控仍然是一个研究难题。针对此问题，本文引入数据挖掘中经典的**SEMMA(Sample -Explore-Modify-Model-Assess)分析范式**并结合网络安全可视化提出一套通用的网络事件分析模型，将分析过程划分为**数据处理、行为特征探索、异常对象定位、异常事件描述与行为模式关联分析等**具体步骤，从而规范安全事件探索分析流程。同时在特征探索环节引用**模糊C均值算法**量化主机行为，识别网络资产结构；提出新颖的可视化表征形式—**PBNLD**( Protocol-based Node Link Diagram) 构建网络通信模型，以提升大规模节点的绘制质量。以安全事件分析模型为指导，面向多源安全日志实例数据，搭建了网络安全事件可视探索系统，通过多视图协同与故事线回溯的方式实现**网络资产划分、网络异常事件提取和攻击事件演化**。最后，实验结果证明分析模型的有效性。



## 2：数据概述
- 本文使用的网络日志数据集取自某高新技术企业公司，该公司内部重要系统被攻击，导致重要数据发生泄漏。日志数据跨度共14天，主要包含三种类型:
-  1) tcpflow日志：该日志为传输层的数据传输记录，包含记录的生成时间、使用协议、目的端口和上、下行数据包的字节数等。
-  2) db日志: MYSQL数据库日志包含记录的生成时间、客户端IP地址、目标地址和用户的SQL语句操作。
-  3) login日志: 记录FTP、SMTP、POP、SMB和数据库中用户登录情况，除基本的信息之外，还包含用户登录名、用户状态(主要包括登录成功、登录失败、密码错误、密码修改成功等) 和用户经过加密后的登录密码。

## 3：安全事件分析模型
- 参照Zhang等在TIFS中提出针对一系列针对网络的全局属性、平均攻击力度的多样性互补指标，本文将提取网络安全事件的关键因素归纳为4W1H (＂When＂、＂Where＂、＂What＂、＂Who＂、＂How＂) ，即:
- 1) 	在过去的哪些时间段，内部网络可能发生过重要事件？这里的重要事件包括系统更新、非工作时间段内的服务器的高负载、数据库的访问量激增等。
- 2) 	在这些重要事件当中，是否存在非周期性活动？这些活动是在网络结构的哪个部位发生的？
- 3) 	针对发现的可疑活动，在网络结构中的主要表现是什么？可疑活动造成的影响有哪些？ 
- 4) 	可否通过日志数据之间的关系映射，定位攻击源？这里的攻击源包括攻击IP、攻击者和攻击类型。
- 5) 	在初步确定了事件发生的时间、位置和内容后，通过对受害资产与内网流量分析能否在一定程度上能还原攻击者的攻击路径与攻击手法？

![事件分析模型](https://github.com/zzhongying/SEMMA/blob/06cf8bb9afb19ade0f97e39af393c608d64ffd93/images/%E5%9B%BE1.png)


## 4：分析模块
### a):基于通信协议的节点链路图—PBNLD
- 客户端常存在与多个服务器进行通信且单个通信对使用协议不一致的情况。若简单使用传统关系图对网络通信流进行可视化，易造成图元重叠，致使很多有效信息被遮挡。为解决上述问题，PBNLD同传统的网络关系图一样，仍使用节点/顶点和连线来编码网络通信之间的关联关系，但其在绘制中引入＂协议＂和聚类计算结果，实现对节点簇的协议分类和信息补充，提升对大量通信网络的节点呈现速率。
- ![PBNLD模型](https://github.com/zzhongying/SEMMA/blob/db422c21264349c2cd31541b321bb856ef9b796b/images/%E5%9B%BE2.png)

### b):企业资产结构分析
- 分析多源日志数据中可能存在的异常安全事件首先要提取网络结构特征，识别并分类企业资产。本文结合嵌套饼图和基于模糊C均值的主机聚类识别网络资产
- ![企业资产结构分析](https://github.com/zzhongying/SEMMA/blob/db422c21264349c2cd31541b321bb856ef9b796b/images/22.png )

### c):基于用户的异常行为探索
- 监测异常活跃主机及数据库访问量的变化情况，可辅助聚焦企业内部异常员工，确定用户行为模式，描绘用户行为画像。按照本文提出的安全事件分析模型，在点击PBNLD中的mysql协议节点后，得到针对mysql协议的各类可视视图，辅助特征探索。
- ![异常用户分析](https://github.com/zzhongying/SEMMA/blob/db422c21264349c2cd31541b321bb856ef9b796b/images/3.png)

### d):基于网络流量的异常行为探索
- 拒绝服务攻击和恶意代码扩散在流量方面体现出一对一、一对多等特征，造成流量异常。对网络流量监控可以帮助管理员快速准确发现网络攻击，分类攻击类型。在对比分析PBNLD中的http协议节点后，发现在对企业多日的流量监控中，企业日常数据流量存在相似性。
- ![异常流量分析](https://github.com/zzhongying/SEMMA/blob/db422c21264349c2cd31541b321bb856ef9b796b/images/4.png)

## 5): 总结
- 文引入数据挖掘中经典的SEMMA分析范式提出了一个通用的安全分析模型，**规范监测和提取异常事件的分析思路**，设计并实现了一个**基于SEMMA的网络安全事件可视探索系统**，系统结合**模糊C均值算法**量化主机行为，识别网络资产结构，并使用新颖的可视化方法—--**PBNLD**来构建通信模型，以此来实现对较大规模通信节点的分析。从结果来看，本文从多种维度衡量安全特征之间的关联关系，有效构建了企业内部网络资产结构。 
结合用户评估，本文将在以下两个方面进行后续改善:
- 1) 完善PBNLD通信模型，考虑将PBNLD中的通信链路与通信频次或通信量进行绑定，更好呈现节点簇中所有的IP节点。
- 2) 在研究中加入机器学习，采用**无监督学习的方式**让安全特征的提取和异常事件的定位更加**智能化**，并允许分析人员在特征选择过程中实时进行交互。

