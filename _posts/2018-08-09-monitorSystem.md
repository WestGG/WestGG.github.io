---
layout:     post                    # 使用的布局（不需要改）
title:      理想监控系统            # 标题
subtitle:   长什么样                #副标题
date:       2018-08-09              # 时间
author:     west                    # 作者
header-img: img/post_green.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 监控系统
---

> 本文主要参考陈皓大哥的文章，在大神的肩膀之上结合自己的想法谈谈监控系统,也算是这段时间以来，对当前工作的一个总结，还请多多指教。

## Why need 监控 ??

#### 监控系统起源与发展

###### 起源
- 分布式系统的出现是由于单机的性能瓶颈。
- 为了解决这个问题，必须将单体应用拆分到不同的机器上，不同的机器提供不同的服务来协同完成一件事。这就是分布式系统的演变过程。随着而来有很多问题，其中有个非常典型的问题即是。
    - **如果分布式系统一台机器出现问题，特别是支付行业，如何及时发现并挽救损失？？**
    - 答案就是**监控**，这就是监控系统出现的最直接原因。

###### 发展
- *那么接下的问题就变成，如何打造一个符合要求的监控系统。为此，我们需要做到的目标为：*
    - **全栈监控**
    
    ![](https://static001.geekbang.org/resource/image/cf/66/cf6fe8ee30a3ac3b693d1188b46e4e66.png)
    - **全链路监控**
        - ***Google Dapper -> 对应的开源实现Zipkin***
        - 国内：美团的 CAT，淘宝 eagle eye，微博的 Watchman，还有听云 Server 等

## 什么是好的监控系统

#### 特征
- 关注于整体应用的 SLA（service-level agreement)
    - 即为对外提供的服务质量，关系到服务的可用性 
- 关联指标聚合
    - 服务的具体实例和主机关联在一起,比如服务有可能运行在JVM OR Tomcat..
- 快速故障定位
    - 关键在于对一个请求的 trace 监控 

#### 监控什么
- **监控对象(全栈监控)**

![](https://static001.geekbang.org/resource/image/cf/66/cf6fe8ee30a3ac3b693d1188b46e4e66.png)
- **请求链路(全链路监控)**

![](https://static001.geekbang.org/resource/image/ab/81/ab79054e0a3cf2d8f1d696e3c367ab81.png)

#### 能力

- 体检能力
    - 性能管理
        - **通过系统大盘，找到系统瓶颈，并有针对性地优化系统和相应代码** 
    - 容量管理
        - 提供一个全局的系统运行时数据的展示，为**扩容OR服务降级**提供决策依据 
- 急诊能力
    - 定位问题
        - **以快速地暴露并找到问题的发生点，帮助技术人员诊断问题** 
    - 性能分析
        - **非预期的性能问题出现时，可以快速地找到系统的瓶颈，并可以帮助开发人员深入代码。** 

## 最终效果

> 当我们做好以上要求的一些细节之后，当问题出现时，能达到的效果
- **了解故障的影响面**：
    - 当一台机器挂掉是因为 CPU 或 I/O ， SQL 操作过慢 过高的时候，马上可以知道其会影响到哪些对外服务的 API
    - 当一个服务响应过慢的时候，我们马上能关联出来是否在做 Java GC，或是其所在的计算结点上是否有资源不足的情况，或是依赖的服务是否出现了问题
- **可以做出调度**：
    - 现某个服务过慢是因为 CPU 使用过多，就可以做弹性伸缩
    - 发现某个服务过慢是因为 MySQL 出现了一个慢查询，就无法在应用层上做弹性伸缩，只能做流量限制，或是降级操作了
- **最终目的 -> 自动化运维**：
    - 自动化扩容 OR 服务降级

![](https://static001.geekbang.org/resource/image/6b/33/6b17dd779cfecd62e02924dc8618e833.png)

## 相关工作

接触监控这一领域实属偶然。目前正在对公司的当前的业务系统进行相关监控，经过一番调研比较，最终选定CAT。[CAT监控系统](https://github.com/dianping/cat)是美团开源的一个监控产品，其核心在于近实时系统监控，请求链路监控，附带的系统状态报表、异常告警显示等功能，

接下来会讲讲CAT能够解决什么问题，以及相关优势和不足：

#### CAT解决的核心问题

近实时的系统监控，附带请求链路。这段话来自于和CAT维护者尤勇大哥的简单交流，也更新了我自己对CAT的更深层次认识。
CAT的近实时的系统监控，与理想监控系统对照，提供了一定的常规体检能力，能让系统管理人员掌握目前系统的运行状态并针对不同情况进行相应的优化。
而请求链路提供了一个极大地好处，那就是帮助开发人员理清一次请求的来龙去脉，无论是阅读代码还是对当前系统架构的理解都大有裨益。

**图为请求链路，很直观的展示了请求的来龙去脉！**

![](https://camo.githubusercontent.com/1fe1b77274569406201c27ed13c8f5a1a12fd346/68747470733a2f2f7261772e6769746875622e636f6d2f6469616e70696e672f6361742f6d61737465722f6361742d686f6d652f7372632f6d61696e2f7765626170702f696d616765732f6c6f6776696577416c6c30332e706e67)

#### CAT的优势

虽然有人觉得CAT的用户界面有点丑，但从本猿的审美来看，竟然觉得有点好看哈哈哈，加句题外话，CAT的代码设计非常优雅，值得一看！

好的正题开始，CAT的优势在于丰富的系统报表，可以是web层的url请求报表，service层的系统rpc报表，dao层的数据库报表，在此基础之上抽象如problem,transation等报表模型，不得不感叹设计之精美。正是报表让我们得以轻松掌握系统当前的负荷状态，结合告警机制，可以说是极大的方便了系统管理和维护。

一句话，CAT的系统监控已经做得非常不错(可以把这里理解为推销CAT，没有广告费)，其对比于zipkin ，其接入难度小很多。

![](https://tech.meituan.com/img/cat/server03.png)

#### CAT的劣势

CAT侧重于‘近实时的系统监控，附带请求链路’。在逐渐深度使用的过程中，发现日志的查看很不方便，因为其附带的报表只能是让管理者看到系统的过去一段时间的负荷，而对于日志搜索可以说是支持非常弱，这也意味着需要配合相关的日志中心，但是如果能把监控和日志相结合该多美好，省去了维护多个系统的麻烦。这也是目前对cat进行二次开发的主要工作。

但这个弱点对比于CAT的优势还是可以被稍微忽视的。

## 总结

- 监控可以分为全栈监控以及全链路监控，CAT侧重于近实时全链路监控 
- 优秀的监控系统应该提供体检能力以及应急能力，以及相关的可用性等等
- 最终讲解了对工作中使用的CAT相关感受，在后续的系列中，会对CAT做个总结，请期待


