# 目标分解与团队形成
## 概念框架
## 目标分解
### 理论篇
下面引入一些理论知识支持后面的`GD`的实现。虽然下面在风格上有点过于正式，但是我们的主要目的是介绍一些相关术语。
- `Goal` : 一个目标`G`是一个或多个代理追求的量化(quantifiable)状态。特别地，一个目标`G`由某个代理或代理组拥有，由一组变量组$V_G$, 我们可以期望的变量范围赋予$V_G$的每个变量。我们用$R_v$表示变量$v \in V_G$的范围。
- `Goal Decomposition Artifact` : 目标$G, S_1 , S_2 .\ .\ .  S_n$的变量集分别是$V_G,V_{S_1},V_{S_2}.\ .\ .V_{S_n}, where\ 2 \leq n \in N$, 因此，$S_1 .\ .\ .  S_n\rightarrow G$ 表示$G$是$S_1 .\ .\ .  S_n$ 父级目标，反过来，$S_1 .\ .\ .  S_n$是$G的子目标。
- 我们应该注意到对于上面的定义应该有一些技术约束。例如。可以接受的重写要求子目标集合地和初始目标共享一些变量，并维持或"缩小"这些变量相关的变量范围。需要确保相关，然而为了简洁，我们省略了这些形式化步骤。
- `Narrowing` 与 `Widening`: $G_1$和$G_2$ 共享了变量, 用$v_i(i = 1,2)$表示变量$v \in V_G$出现在$G_i$中，于是，如果$ R_{v_1} \supset R_{v_2} $, 我们称$[G_1;G_2]$为$G_1$在$v$上的`Narrowing`, 表示为$G_2 \rightarrow G_1$;反之称为`Widening`, 表示为$G_1\rightarrow G_2$ ,我们可以重载上述的术语，将它们引用在流程和结果(the process and result).
- `目标细化与抽象(Refinement and Abstraction)`:  我们把目标分解或者单独或者组合运用的目标收缩(Narrowing)称为`goal refinement`;把目标集的合成或者单独地或者组合地某个目标的扩张(Widening)。我们通常抛弃有限定符的目标，重载每个术语来表示过程和结果。因此，更明确点, 一个全局目标重新描述为子目标的组合不仅仅是分解，事实上是目标精细化。然而，为了保持和CrossWork的术语，的确通用的术语一致，我们保持使用非正式术语目标分解
### 一个目标分解的流程
 本节详细阐述了特定目标的分解过程。我们通过对细节，输入输出，流程本身的简单描述实现了该过程，最后我们对实现进行总结。
 - `Input Structures`: 订单规范(Order Specification)是目标分解模块输入的最重要部分， 订单规范说明了指定数量的所需商品, 规定了在订单上的所有约束(比如，规定在15天内交货)，允许提供任何的附加信息(比如以前的相似订单)。而对于当前的讨论，最重要的一个方面是产品规范。
 - `Output Structures`: 目标分解`Module`输出一个目标分解`Artifact`:　一组`Components` + 一组`Primary Services`. 特殊地，这些本体逻辑(ontology)的实例来自市场和产品的本体逻辑,适当地。
 - `Process`:　输出是通过以下目标分解过程的实现从输入中导出的。我们从订单规范中分析产品规范（或这些规范的集合；我们假设一个产品规范用于讨论，对许多产品都有明显的概括）。根据产品本体中可用的信息和结构，产品被（物理上）分解为尽可能精细的细节。为了促进这一点，产品规范包括产品概念的特定规范，该规范可以从产品本体中获得或改编。
### 实现篇
### 目标分解的例子
## 团队形成