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
### BPEL Language
- BPEL，也被称为BPEL4WS,WSBPEL,是Web Service Specification之上基于XML的语言，用于定义和管理长周期服务服务编排或者流程。
- 在BPEL中，业务流程是粗粒度有状态的服务，执行一系列步骤去完成业务目标。目标可能是业务事务的完成，或者服务作业的完成。
- BPEL 中的活动会调用伙伴的服务去执行一些任务，然后返回结果到流程。
- The aggregate work, the collaboration of all the services, is a service orchestration.
-  如果BPEL用于服务编排, 主要的服务是业务流程；
- BPEL还结合XPATH来编写表达式和查询；
- Web Services对BPEL很重要性有两点：
  - BPEL流程遵循WSDL服务模型:　这是为什么业务流程实现为服务。更具体的说，BPEL流程包含WSDL定义，被实现为被实现为WebServices(一般遵循WSDL,SOAP,UDDI...). 服务定义，发现，调用的基本函数使得WebServices实现允许BPEL执行引擎可以在运行时领用其他的WebServices标准，比如`WS-Addressing, WS-Coordination`。
  - 协作的服务(Collaborating Services)也是Web Services: 因此，BPEL流程和collaborator都是WebServices. BPEL执行引擎包括Web Services运行时组件。
- BPEL模型很好地从协作服务(paticipating services)中分离了协编排逻辑(orchestration logic)，相比与服务交互的硬编码，BPEL流程配置提供了许多优势。然而这样却存在处理负担和基础设施费用，所以BPEL可能不是简单编排的最好选择。根据经验(As a rule of thumb)，一个简单的编排包括2到5个服务，有静态的交互模式。作为开发流程的语言，BPEL擅长执行按时间先后发生活动序列，以及和内部或者外部的服务交互。这些流程可能表示一些 IT场景(IT Scenarios),比如整合(integration)，业务场景(Business Scenarios),比如信息交换(information exchange),  工作流(flow of work)。
- BPEL的局限：
  - BPEL没有考虑到人在流程中的地位，因此没有提供一个传统的业务流程管理，比如这里没有角色，任务，邮件这样的概念。
  - BPEL不支持复杂的业务流程，这些业务流程在执行过程中不断演进，并扩展为结合新的参与方和新的活动。
  - BPEL不支持业务活动(Business Activity Monitoring(BAM)),没有针对监控模块的数据模型

- BPEL SERVER:
    - `Core BPEL engine`:the runtime environment where the BPEL processes are deployed and executed.
      - support for the process life cycle requirements (instantiation, communication, dehydration/hydration, correlation, transaction management, compensation and termination) as described in the
specification.
    - `WSDL bindings`: communication with the BPEL processes deployed on the server
      -  clients that would like to access a BPEL process.
      - BPEL processes that would like to access other Web Services (partner links).
    - `Integration Services` : performing transformations of XML documents that go beyond the support of XPath
      - 这对于在BPEL中描述的业务流程与Web服务通信和交换XML文档时非常有用。
### BPEL Processes and Activites
ActiveBpel Engine
### Global Enactment and Subsystem
Workflow Composition module模块创建流程定义，Global Enactment module则是执行这些流程定义的运行时环境。Global Enactment module负责收集和存储运行过程中所有监控信息；
- 部署流程
- 执行流程
- 编排所有Local Business Processes的执行
- 收集和存储所有(global and local)的监控细节信息
-  告知用户执行状态(at global and local level)
- 分析和传递任何控制用户命令到Local Business Processes

Global Enactment module接受两类输入：
- ` global process definition ` : 已验证的，有效的BPEL流程文件
- `WSDL Interfaces`:  描述了全局流程如何被外部世界访问

Global Enactment module提供两类输出：
- `SOAP messages` : 描述了如何调用和编排WebServices(Local business process)
- `XML files`: 包含与(all levels)执行状态的相关的结构化信息

Global Enactment module需要用户接口，原因有二:
- 需要用户提供所有全局变量实例化或者更新的表单；
- 需要给用户关于错误或者异常的反馈信息

Global Enactment module与其他模块接口交互的接口:
- ` Workflow Composition module `:
  - allow the transportation of the composed, finalized and verified Global Business Process to the enactment engine hat will execute it.
- ` Local Enactment module`:
  - allows the invocation and, in general, orchestration of all local business processes by the Global Enactment module.
  - also allows the transportation of all monitoring details between the global and the local level of execution.
- ` Global Monitoring UI module `:
  - allows the transportation of all structured monitoring information to the UI that will depict the current status of execution in a graphical way.
  - enabling the user to instantiate the execution of a global process definition as well as controlling the execution.
## Local Workflow Enactment
Local Enactment module是局部业务流程定义的运行时环境，这些局部的业务流程定义由IVE成员的专用的工作流管理系统(WFMS)或者其他工具开发和管理。另外，Local Enactment module会负责告知Global　Enactment module 局部业务流程执行状态。
- 执行局部流程
- 告知local supplier执行状态(local level)
- 告知Global Enactment module的执行状态(local level)
-  如果必要，调用local Legacy Systems

 Local Enactment module需要一些输入:
 - `SOAP messages` : 触发局部业务流程的执行
   - allow an external party to request the specification/definition of a local business process
   - also allow an external party to issue control orders (stop, pause, resume) to a local business process being executed
 - `XML documents `:表示Legacy system（例如，EIS）对本地业务流程执行请求的操作的响应。

 Local Enactment module产生三类输出:
 - `SOAP messages `: 报告局部业务流程的执行状态
 - `BPEL files`:  描述局部业务流程的规范/定义
 - `XML documents`: 表示对Legacy system的请求

Local Enactment module需要两类用户接口:
- `form interfaces`
- `feedback messages interfaces`

Local Enactment module与其他模块交互的接口:
- `Global Enactment module`:
  -  allows the invocation of a local business process by the Global Enactment module;
  -  also allows the transportation of all monitoring details between the global and the local level of execution
- ` legacy systems `
  - allows the connectivity and management between the Local Enactment module and heterogeneous EISs, which provides the information infrastructure for an enterprise

Local Enactment module的子模块
- `local WFMS`:
- ` BP-WS interface`: BP-WS supports an internal business process specification that can be accessed externally through a number of dedicated Web Service interfaces.
  - `Invoke and reply interface`
  - `Interface to obtain the business process model`
  - `Interface to monitor the execution of the internal process`
  - `Interface to control the execution of the internal process`
## Coupling to Legacy Enactment
为了 完成IVE的流程运行，不得不考虑IVE成员的EISs与Legacy systems.　EISs为企业的业务流程提供了关键的基础信息设施。EIS与Legacy applications包括关系型数据库系统(relational database systems)，企业资源管理系统(enterprise resource planning systems)，主机事务处理系统 (mainframe transaction processing systems). Legacy systems包括组织的业务逻辑，蕴含了多年来对系统的编码，开发与完善工作。但是，它们通常是无文档，紧耦合，不够灵活。因此，很难将它们整合到协作工作流中。出于大多数公司在遗留设施和管理上投入巨大的原因，对遗留环境进行重构或者重大的改动都是不切实际，也是不现实的。
CrossWork提出的整合方案：
- Integration of the existing legacy systems and EIS by means of  providing them with a uniform Web Service interface that can be integrated into Local Workflows

Legacy Integration module:
- 将遗留系统整合到新的业务流程中允许从遗留企业系统中创造出新的价值,使得他们可以在局部和全局级别向外部有选择地公开自身细节，并参与到IVEs的业务流程协作。
- 向 Local Enactment module提供了统一的接口用于在异构信息系统上的执行事务,同时隐藏了在每个具体EIS上的操作细节。

Legacy integration方案考虑的方面：
- `Heterogeneity and complexity of EIS`:
- `Transactional access to EIS`:
- `Secure access to EIS`:
- `Scalability`:

 Legacy Integration module的功能:
 - providing a Web Service interface allowing connecting and executing business processes into multiple and heterogeneous EIS and legacy systems.
 - validating requests to a concrete legacy system, avoiding unnecessary calls to this system.
 -  transforming both requests and responses to the legacy system, to facilitate the integration with the Local Enactment module.

 Legacy Integration module输入:
 - `SOAP messages` : 描述执行的业务流程
   - 指定执行操作的EIS,执行的业务流程
- `XML document`: 业务流程的请求文档
 Legacy Integration module输出:
 - `SOAP messages`: 表示系统对请求的业务流程执行的响应。

Legacy Integration module只有功能接口，没有用户接口。Legacy Integration module 和Local Enactment module交互，Local workflow enactment请求legacy interface在EISs上执行一些操作。

Legacy integration module internal architecture:
- `Legacy Integrator EJB`:contains the business logic of the Legacy Integration smodule
- `Configuration`:stores EIS information
- `Message Validation`
- `Message Validation`
