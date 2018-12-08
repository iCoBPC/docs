# Business Process Composition
业务流程组合是一种在动态虚拟组织中自动构造业务流程的方法。
业务流程运行在工作流中，这可以由工作流管理技术支持。

## Perspectives for Business Process Collaboration
业务流程的协作可以依靠链接下层负责执行局部业务流程的工作流管理系统。为了以合适的方法使局部工作流互联，一些冲突问题需要解决。
- `Privacy and Security` :
  - 每个伙伴都希望去尽可能的对外隐藏自身内部工作流，然而为了告知合作伙伴自己的业务进展，便于全局业务流程的追溯，不得不暴露自己内部流程的一些细节。
  - 因而，我们将局部工作流分为两个可见级别：`conceptual(internal) level` 和`external level`，概念级别的工作流对伙伴和外部世界可见，内部级别则只对执行改工作流的伙伴可见。
- `workflow perspective` : 传统的工作流视角主要是`control flow` , `data flow` ,`resource flow` , `transaction flow`.
- ` overarching perspective ` : 跨越多个组织的工作流的总体视角，给定一些伙伴的外部级别的局部工作流，怎样去构造全局工作流(eSML表示的)。构造的全局业务流程可能存在一些错误，比如`deadlock`，两个业务流程都在的对方流程的下游待执行任务发送信息过来。这样的错误需要被尽早检测到。利用`Verification`工具自动检测，同时借助`prototyping`工具人工模拟工作流执行，在全局工作流工作　之前检查出存在的逻辑错误。

## Process Specification
eSML : 定义业务流程协作
XRL: 一种工作流语言，作为eSML的一部分，定义了流程
### eSML
eSML用来构造外部级别的全局工作流模型，业务流程细节会被公开。
eSML中包含很多高级结构：`visibility`,`monitoring`,`polling business collaborations`;
#### Foundation of eSML
eSML用于设计`electronic contrcts`,电子合约旨在使用信息科技去提高纸质合约的有效性和高效性。在最高级别的抽象中，eSML形式的合约可以简化为回答三个问题:
- `Who`:  规定了参与合约制定和发布的人；
- `Where`: 规定了合约的上下文；
- `What`: 建模交换的价值，规则，用XRL定义的流程；
`How`指定了合约内容结构，表示，指定和发布，但是这没有囊括在eSML内，已经由CrossWork的系统实现。
#### The Who Concept
- `parties` : 一些声明了权利和义务的actors，一份contract至少有2个parties，另外可能还有被称为mediator的第三方,但是他们的权利和义务没有声明，mediator不是必须的。
- `company_info`:　每一个签约方或者中介者相当于一个`company_info` ，`company_info` 包含1个`company_data`,1个`company_contact_data`,多个($\ge$0)个`resource_section`,多个($\ge$0)个`context_section`;
- `company_data`: 包含信息比如: `contract_party_name / mediator_name` ,  `legal_org_type`...
- `company_contact_data`: 指的是和`eSourcing party`的地理位置相关的信息；
- `resource_section`: 资源视角的根类，协约方或者中介方都可能有相关的资源被涉及，多数情况，定义中介的资源是多余的(superfluous),除非中介在商业交易中起到主要作用。
- `only_vars_section`: `company_data`和 `company_contact_data`的父类，contains  `var_section` ,  many ($\ge0$) `snippet_section`...
- `var_section`: 属于数据流视角，　includes `company_description` , `trade_registration_number` ,`VAT_registration_number`,`addr_of_registration`....
- `snippet_section`: 引用了`contract snippets`, 这些合约片段对应特定的合约定义，比如代表常用的术语和条件。

#### XRL—eXchangeable Routing Language
　XRL是一种基于实例的工作流语言，使用XML表示流程定义，Petri nets表示其语义；
## Workflow Composition
在team一起工作之前，每个伙伴的局部的外部级流程需要被编织在一起，工作流组合模块如此来构造全局工作流模型，全局工作流用eSML表示，以协调每个成员的局部工作流
### Basic Approach
- 分析局部工作流的依赖关系：
  - 每个局部工作流被看做一个黑盒子(black-box),　启动之前需要特定输入，完成之后给出输出，从而隐藏局部工作流的细节。
  - 全局工作流激活局部工作流，同时需要考虑不同局部工作流之间的数据依赖，必要的时候，需要修改控制流。
  - `Dependency graph(L , E , fork)`: 数据流的依赖关系可以从依赖图中分析得到，$L$表示局部工作流集合，$E \subseteq L \times L$ 表示依赖关系这些工作流间的依赖关系,$fork : L \to \{AND ,XOR\}$表示每个工作流的`outgoing dependencies`的分支类型。这些边表示的是数据流不是控制流。
  - 依赖关系可以通过分析局部工作流的所需的外界输入，以及他们对外界的输出得到，但是，由于是特定领域问题，分支类型需要由用户提供。
- 构造满足数据依赖的全局工作流:
  - eSML是一个结构化语言，互斥和并行网关需要调整，dependency graph是非结构化的。不需要调整网关。平衡结构需要从非平衡结构派生, 因而二者之间并不直接翻译转化。这里需要某种转化算法，将依赖图转化为在eSML中定义的全局工作流。
### Extension for Basic Approach
黑盒方法在一些场景中过于粗粒度(coarse-grained)，比如伙伴需要看到局部工作流的进度的内部细节，可是，黑盒方法不会公开任何内部细节，基于此， 需要使用白盒方法，将局部工作流抽象为外部工作流。
- 依赖不再是不同的局部工作流之间，而应该是不同的局部工作流的活动之间；
- 构造的全局工作流必须满足所有这些依赖，但是也不许使得每个局部工作流的完整性。将依赖从活动级别传播到工作流级别，然后再使用黑盒方法即可。

## Workfow Validation and Prototyping

## Interfacing to WFMS
要执行eSML定义的全局工作流，需要将它转化可被工业界最先进的发布工具处理的语言，这里选择了BPEL.　这里需要将eSML Specifications 映射为BPEL Specifications.
- BPEL 主要是为了协调Web Services ，　而eSML是为了在外部级别定义协调器eSourcing的配置
-  eSML的主要目标是为组织间业务流程建模提供高水平的适用性和表达力。
- 实际上，这意味着BPEL指定单个伙伴的视图，即一组服务的协调器，而eSML指定所有伙伴的服务编排的全局视图，也包括每个伙伴的协调器(orchestrator)
- eSML支持业务规则，BPEL不支持
- 相比于BPEL, eSML中数据流和资源视角更清晰;

上面比较可以看出，不能将eSML一对一映射到BPEL,而应该从二者共有的主要视角出发，进行映射，主要包括控制流，数据流，资源三大视角。
- 主要映射部分是将eSML的控制流部分XRL映射为BPEL;
- 关于数据流和资源视角，每个eSML中的变量，都会被转化为BPE变量。对于每个参与方(resource)会定义,一个BPEL parternal link。`Partner Links` 定义了伙伴的局部服务怎样被链接到全局工作流
- 虽然eSML其他视角的的信息,没有出现BPEL中，但是这些信息可以用来配置某些模块，比如监控全局工作流的模块。

The BPEL specification focuses on the coordinators view
of the global VE workflow, whereas the eSML specification focuses on the global
view of the entire workflow
