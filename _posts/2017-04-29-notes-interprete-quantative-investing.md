---
layout: post
title:  "《解读量化投资》—读书笔记"
subtitle:   "西蒙斯用公式打败市场的故事"
author: "MakerChen"
# header-img: ""
date:   2017-04-29 15:30:00
tags:
    - notes
---


#### 解读量化投资--西蒙斯用公式打败市场的故事
作者-忻海  

读书笔记

---

### 只赚不赔的好买卖
* 投资方法可以细分为基本面判断法、基本面量化法、技术判断法和技术量化法。
* 大奖章基金投资必须符合三个条件：
  - 必须在公众市场进行交易
  - 必须具有足够的流动性
  - 必须适合用数学模型来交易
* 模型一：某个期货的价格在开盘的时候远远高于前一天的收盘价，那门他们就会沽空这个期货；如果开盘价远低于上一天的收盘价，那门他们就买入。
* “热土豆假说”：本来只有一个土豆，但是银行之间互相询价，导致原先那笔交易的市场作用被放大。
* 套利操作使价格更为合理
* “无套利理论”，现价金融理论的一个基本出发点。
* 没有风险的超额回报是不存在的。
* 统计套利的原则来自统计学里的大数原理。

### 华尔街最牛的人
* 市场的不理性的时间可能要比你保持不破产的时间长。
* “自我实现预言”，某个预言被提出来之后，人们都认为这个预言会实现，所以相应调整自己的行为方式，使得预言真的实现了。
* 任何一种金融衍生工具，只要某些假设成立的话，都可以通过一个固定的模式来进行复制，而复制的成本就是这种工具的市场价格。
* 杠杆的好处是：如果成功的话，它会将你的成功放大很多倍；杠杆的坏处是：如果失败的话，它会将你的失败变得无可挽回。
* “肥尾分布”

### 我心里埋藏着小秘密
* 不要把所有鸡蛋放在同一个篮子里
* 要训练模型，要迭代、要收敛。
* 西蒙斯关注的是许多很小的机会，把每一个单个的投资能否取胜比重要；巴菲特关注的是数量不多的，比较大的机会，每一个投资都很重要。
* 没有一个长期不变能赚钱的模型
* 诉讼中提到的策略
  - 使用电脑程序自动搜寻一家专门为机构投资人买卖股票牵线搭桥的电子平台上的数据，从中推断潜在的秘密信息，进而通过交易获利。
  - 限价买卖指令表。
  - 掉期合同
* 基金越大，要想达到好的回报就很难。
* 交易策略以短线为主
* 使用“每笔交易数据库”（记录每一笔交易的价格变化，而不是每分钟的价格变化）；很可能使用限价指令表之类别人比较少用的数据进行分析。
* 模型用的不是很高深的数学，但是用很复杂的统计学工具。
* 使用语音识别的分析方法来分析数据，很可能是统计信息论、最大墒及隐含马尔可夫模型。
* 使用不止一个交易模型来获取信号；交易模型不断变化。
* 在交易不同的金融工具和不同的基金中可能使用不同的交易模型（大奖章使用短线交易模型，机构投资人股票基金使用中线交易模型）
* 对交易工具的流动性问题的考虑占很重要的位置。
* 借鉴别人的好办法，获取行业信息，但是对外实行信息封锁。
* 不断探索新的投资方式、工具。

### 股价上的风暴
* 量化投资一般是指使用数学工具来投资的方法，跟凭借判断来投资的判断投资是一组相对的概念。
* 方差-协方差矩阵、分型几何
* 趋势模型，最近涨的东西今后更可能涨，最近跌的东西今后更可能跌。
* 双移动平均曲线法、通道突破系统
* 趋势模型与有效市场假说背道而驰。
* 金融投资领域有理论和实际不符的地方，哪个赚钱哪个说了算。
* 有效市场假说：市场上所有已知的信息都已经被合理利用了，但不能解释，规模异常、价值异常和动量异常（额外的回报是存在特殊风险的）。
* 在对待赚钱机会的时候，人们一般会避开风险；而对待赔钱机会的时候，人们却又主动寻求风险。
* 尽量不要止盈；一定要止损、止损、再止损。
* 大部分的股价上下起伏都是无法用投资者的理性行为来合理解释的。
* 虽然有效市场假说有很多问题，但目前仍然是我们分析判断金融投问题的出发点。

### 趋势是你的朋友和敌人
* 模糊逻辑：一种进行逻辑判断和运算的方法，假定各种概念不能完全用“是”或者“否”来代表，而可以是70%的“是”，30%的”否“。
* 神经网络：将许多小模型组合起来，最后交给“大脑”统一判断。
* 遗传编程：让很多模型自由突变、组合，通过现实数据来寻找最能适应环境的模型组合。
* 隐含马尔可夫模型：某种价格或者系统从上一个状态到下一个状态的随机过程。
* 子波变换：通过制造出一些特定的波动，让这些波动与研究对象互动，最后分离小波，就携带了研究对象的一些信息了。
* 贝叶斯网络： 一系列不确定的数据和过程之间的可能存在的因果关系，并不断更新。
* 分型几何：
* 聚类分析：按照数据和一些事先制定的标准将各种不同的对象分成不同的组群。
* 伯乐坎普的统计信息理论可以去掉很多噪声，有效地寻找真正引起价格变化的那些条件。

### 更高、更强、更快

### 飓风里行船只往后看
* 谎言有三种：谎言、该死的谎言和统计数字。
* 数据挖掘与数据窥视（将巧合当成规律）
* 量化分析的过程其实就是通过过去的数据窥视将来的过程。

### 谁有下一个点石成金的手指

### 总结：
经友人的介绍读完了本书。更像是一本量化投资发展的历史书，阅读起来并不难啃。完成通篇阅读后，能初步窥探到量化投资的一个基本轮廓。就全书来看，该书是比较推崇量化投资方式的，但同时也指明了其复杂的前置条件及深远的后续影响。里面也简单的提到了各种投资方法供参考、研究，也同时指出投资模型需要不断的更新才能适应市场规律、持续盈利。文末也提到了量化投资方式对新兴的中国市场抱有乐观态度。

但个人愚见“投行有风险，入市需谨慎”。本书作为了解量化历史，有很大的帮助及参考意义，但是否就因此进入量化领域，还是需要自己评估。不可否认的是这些由数学家、诺贝尔奖得主等组建的团队，有些也还是跌倒了在量化的路上。腹黑的说法，“如果真能赚大钱，你写书干嘛？”。哈哈～～

不管怎样就像书里所述的“有人说好得不得了，有人则不屑一顾”，不可置评。多一种选择未必是个坏事，只要不对它产生太强的依赖。