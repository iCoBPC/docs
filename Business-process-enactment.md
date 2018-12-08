# Business Process Enactment
[TOC]
## Overall Approach to Enactment
BNP(Business Network Process)包括两级机制，`全局业务流程编排(orgastration)`和`局部业务流程执行`,  通过`Remote Workflow Client`机制，各级都具有灵活的发布拓扑结构。
为了保证与工业标准流程运行平台的互操作性，流程运行使用了工业标准BPEL。BPEL建立于SOA与Web Services Specifications，用于定义和管理长周期的服务编排或者流程。在BPEL中，一个业务流程是粗粒度Web Services，它通过执行控制流来完成业务目标，在控制流中步骤会执行一些活动。这些活动主要是调用伙伴提供的服务来执行任务并返回结果到流程。
驱使我们选择BPEL的原因有很多：
- 企业的SOA实现正在从简单，细粒度的服务向更复杂，粗粒度的服务演化；
- 企业正在使用面向服务的架构策略进行整合。
- 为了迎合上面两点，供应商正在创建整合和SOA基础设施的解决方案。这些解决方案提供了BPEL编排，或者使用BPEL进行内部处理。
- BPEL2.0规范成熟;

**`Global Workflow Specifications(Business Network Process Specification)`**
- 由全局架构中的子模块eSML2BPEL自动翻译eSML得到的BPEL
- 该子模块是IVE formatio n与IVE  enactment的桥梁

**`BPEL Engine`**
- 实时发布运行全局工作流
- 读取流程定义文件以及其他一些WSDL文件，创建BPEL流程定义实例
- 当事件来了，会触发开始活动，创建流程实例，并启动。
- 引擎也负责持久化，消息队列，异常，及其他执行细节。

**`BP-WS Interface`**
- 在全局流程运行过程中，它依赖控制流，会同步或者异步调用`local partner Web Services(local business processes)`去执行一些作业。但前提条件是`local partners`已经暴露它们的业务流程作为Web Services,并且他们有WSDL接口。然而，这是不够的。
- 在`Dynamic Business Network Process Management(DBNPM)`中，相比与传统的Web Serivces, 代表业务流程的Web Service经常需要提供对内部流程的可见性。出于这个目的，我们使用`BP-WS`
- `BP-WS`包括可以被外部访问的`business process specification` 和`business process state`。外部可以痛一些专门的WebService接口访问这些规范和状态。利用`BP-WS classes`，每个`local partner`可以自主决定暴露或隐藏服务的细节。

**`monitoring of the enactment at both local and
global levels`**
-   为`local Web Service`建立`business process specification(in BPEL)`，可是实现从局部和全局两级整体监控流程的运行。
- `stateful global BPEL specification ` 和`stateful local BPEL specification `需要合并起来，在我们的方法中在`global Enactment Engine`  中扩展了`monitoring and control`的功能：添加`Monitoring and Control Engine`
- 目的有两点:
  - 为了感知流程执行的整体状态，监控引擎可与局部和全局的所有服务的规范与控制端口通讯;
  - 为了通过控制端口发布控制信息发哦合适的的局部服务；
##  Global Workflow Enactment
## Local Workflow Enactment
## Coupling to Legacy Enactment
