+++
title = "使用beancount记录证券投资"
description = "了解bean 记录证券，计算股价和成本的表达方式"
date = 2021-11-23T15:14:00+08:00
publishDate = 2021-09-10T18:53:00+08:00
lastmod = 2021-11-23T15:14:10+08:00
categories = ["学习笔记"]
draft = false
authors = ["iTBoyer"]
+++

<div class="ox-hugo-toc toc">
<div></div>

<div class="heading">&#30446;&#24405;</div>

- [目的](#目的)
- [成本和价格](#成本和价格)
- [豆子库存](#豆子库存)
    - [库存自动化管理的方法](#库存自动化管理的方法)

</div>
<!--endtoc-->


## 目的 {#目的}


## 成本和价格 {#成本和价格}

1.  成本cost（ `{}` ）：当你买入一支股票的时候，每股所花的钱即是成本，表示方法： {}。

2.  价格price（ `@`: 单价 `@@`: 总价 ）：我们通常把某支股票的市场价（market price）称为它的价格。

3.  盈亏（PnL）：cost 和 price 在那一瞬间是相等的，在那之后，cost 不变，price 有涨有跌；在卖出时，cost 和 price的差价，就是每股的盈亏（PnL）。


## 豆子库存 {#豆子库存}

每一个豆子，在买入时，会指定豆子的种类和三个标签（成本、日期、备注），用来描述豆子的唯一特性。  

例如下面，两次买入的豆子的语法：  

{{< highlight beancount "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
7 AMZN {1000.00 USD, 2017-01-14}
5 AMZN {1575.39 USD, 2019-01-04}
{{< /highlight >}}

当卖豆子时，也必须通过种类或三个标签，指定要卖出哪些豆子。  

例如：~4 AMZN {1000.00 USD}~  


### 库存自动化管理的方法 {#库存自动化管理的方法}

对于 held at cost 的证券，Beancount 提供了 5 种簿记方法：  

1.  STRICT：每次取豆子时，必须要明确匹配到某个 lot，不允许模糊匹配（例外是 {}，代表全部取出）。这也是默认的模式。
2.  FIFO：即先进先出（First-In, First-Out），每次取豆子时自动从最老的豆子开始取，直到取光。
3.  LIFO：即后进先出（Last-In, First-Out），与 FIFO 相反，最新的豆子优先被取出。
4.  AVERAGE：每次重新计算 cost basis。该方法暂未实现。
5.  NONE：完全不合并，允许不同符号的 lot 同时存在。

比如你可以改用 FIFO 簿记方法：  

{{< highlight beancount "linenos=table, linenostart=1, hl_lines=0-0 0-0" >}}
1970-01-01 open Assets:Trade:Positions "FIFO"
{{< /highlight >}}

在使用 FIFO 簿记方法之后，你只需要写 -4 AMZN {}，Beancount 就会自动取出最老的 4 颗豆子。  

盈亏受5 种方法的影响：  

假设今天 AMZN 的价格是 1300 USD，你希望卖出 4 颗豆子。如果你选择按 FIFO 卖出标签为 {1000.00 USD, 2017-01-14} 的豆子，那么你的 PnL 是盈利 1200.00 USD；如果你选择按 LIFO 卖出标签为 {1575.39 USD, 2019-01-04} 的豆子，那么你的 PnL 是亏损 1101.56 USD。同样一笔交易，按不同的簿记方法，一个是盈利，一个是亏损！  

谁会来关心你的 PnL 呢？除了你自己，当然是税务局了。比如 IRS 就是用 FIFO 的方式来计算 PnL 并据此征税的。对于投资者来说，盈利的话要缴税，亏损的话可以抵税；长期持有的股票卖出时所得税率低，短期持有的股票卖出时所得税率高——只有使用规范的 PnL 计算方法，才能算出正确的税务数据。
