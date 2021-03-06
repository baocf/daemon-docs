

###用户分类以及用户活跃度的衡量方法

-------------------

__活跃度计算方式__

- 停留时间:即用户开启app后使用的时长
- 开启次数：从用户第一次开启到某个时间段共开启的次数
- 日活跃用户：当天启动过app的总用户数（包含老用户和新用户）
- 周活跃用户:一周内启动过app的总用户数（包含老用户和新用户）
- 日活跃率：当天活跃用户数/总用户数
- 周活跃率：一周内启动用户数/总用户数

----



**活跃度的衡量方法**

一个APP最根本的便是用户，那么当然用户也分许多种类，比如活跃用户、留存用户、流失用户等等，那么一般情况下又是怎么来分析活跃用户的呢？首先，我们来看一下用户的具体分类。

##不同类型的用户

用户包含各种类型，反应了不同群体的特征和想法。在使用整个产品的周期中，我们应定义更全面的指标：

- **流失用户**：有一段时间没有再打开产品，那么我们就视为流失用户，根据产品的属性，可以按30天，60天，90天等划分。

- **不活跃用户**：有一段时间没有打开产品，为了和流失区分开来，需要选择无交集的时间范围。比如流失用户是60天以上没打开产品，那么不活跃则是0～60天没打开。

- **回流用户**：有一段时间没用产品，之后突然回来再次使用，则称为回流用户。回流用户是活跃用户，且是由流失用户或不活跃用户唤回而来。

- **活跃用户**：一段时间内打开过产品。

- **忠诚用户**：也可以叫超级活跃用户，长期持续使用产品，比如连续四周，或者一个月内15天等。



--------------------------



现在我们发现，不论是活跃用户还是不活跃用户的维度，都一下子丰富了起来。

**活跃用户**

很多人对于什么是活跃用户的概念还很模糊，大多数运营数据分析平台上都直接给出了一个活跃用户的数字，那么什么是活跃用户呢？ 用户每天既会不断新增，也会不断流失，如果单独只看每日活跃用户数，是很难发现问题的本质的，所以通常会结合活跃率和整个APP的生命周期来看。

2活跃率是指活跃用户/总用户，通过这个比值可以了解你的用户的整体活跃度，但随着时间周期的加长，用户活跃率总是在逐渐下降的，所以经过一个长生命周期（3个月或半年）的沉淀，用户的活跃率还能稳定保持到5%-10%，则是一个非常好的用户活跃的表现，当然也不能完全套用，得视产品特点来看。

概括来说，增长黑客在衡量“活跃度”时，会通过访问次数、访问时长、收藏指数和相关影响因素几个维度来判断用户的活跃情况，并按连续活跃天数和阶段内任意活跃天数2个时间维度对用户活跃度进行整体分析。

__如何进行用户活跃度分析？__

> 1.通过日访问次数评估用户活跃度

访问次数代表用户每日使用产品的频次，我们将计算所有访问次数大于1次的用户，来计算这些用户的平均访问次数，或者设定一个固定值，例如某位用户连续7平均访问产品的次数为3-5次，那么我们就可以把4设定成固定值。

在基于访问次数计算出平均值或设定了固定值（A）之后，我们要统计每日访问次数大于1次并且小于A50%的用户，以及每日访问次数大于A50%并小于A的用户，以及日访问次数大于A的用户分别的数量。统计的目的在于通过访问次数将用户进行分层，高于A值的用户是我们产品的绝对活跃用户，对于没有达到A值的用户可以通过运营活动重点提升日访问次数。



> 2.通过日访问时长评估用户活跃度

与通过访问次数计算用户活跃度的方式相同，找出用户使用时长的衡量值B，通过B对用户进行筛选分组。

> 3.通过“收藏指数”评估用户活跃度

一般来说，用户产生收藏行为代表着对产品内容的认可，如某篇文章，某件商品。

在实际统计中，“收藏指数”因产品所在行业的不同而不同，需要针对产品所在行业进行个性化定义，例如社区类产品要统计有过发帖、回帖行为的用户，资讯类产品要统计日浏览文章大于5篇（举例）的用户，电商类产品要统计日浏览商品大于5件（举例）的用户等。

同样，通过对“收藏指数”的定义，计算出相关用户行为的衡量标准，我们称其为C值， C值为活跃用户必须满足的条件，继而可以筛选出高出C值50%、100%甚至更多的用户数量。

> 4.找出用户活跃度的影响因素

在影响因素这项分析上，需要统计不同特征用户的活跃度人数。例如领取红包后1-3天的活跃用户变化情况，与非领取红包用户的活跃度进行对比，再比如连续打卡3天、5天的用户在活跃度上的变现差异。

以上是4项用户活跃度的衡量方式，在统计时，首先需要筛选出满足条件的用户数量，然后计算满足条件用户在整体用户中的占比。

