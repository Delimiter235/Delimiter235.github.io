## 依据以及需求
### 什么是 IPO 图
IPO 图，全称 Input-Process-Output 图，即”输入-处理-输出“图，是软件工程和结构化设计中，用于对每个模块进行详细设计和描述的图形工具。
### 为什么要画 IPO 图
我最近正在尝试模块化和工程化重构一个项目中的脚本文件。

## 构建思路
在当前重构阶段，为了优先从宏观架构层面审视模块解耦与职责边界，我采用了“黑盒抽象”的设计策略：隐去函数内部的具体执行细节，仅形式化定义其入参（Input）与返回值（Output）契约，因此本图在严格意义上是一个 IO 接口规格图。

与此同时，为避免图像过于复杂，本次暂未引入全量数据流图（DFD），而是聚焦于函数调用图（Call Graph / Invocation Diagram）。这种视角能够最直观地暴露函数间的上下游调用拓扑、跨模块调用关系以及潜在的冗余代码，为后续将面向过程脚本重构为高内聚（如磁盘读写与计算业务分离）、低耦合（模块间传递简单数据）的模块化系统奠定基础。


## 各个模块以及说明
各个模块如图 1.1 图例部分所示，IO 图主体部分则是基于最近需要被优化的项目内容。
<div align="center">

![ipo chart](https://github.com/Delimiter235/Delimiter235.github.io/blob/main/images/wsl_blog/case_report_analysis_structure_v1.drawio.png?raw=true)
图 1.1 OCR 表格结构化解析管道系统 IO 图
</div>

### 1. 外部依赖（External Dependencies）
白色矩形容器，系统所依赖的外部标准库（主体部分显示为json、pathlib.Path），直观呈现了系统的物理 I/O 边界。
### 2. 模块文件（Files）
有黄色顶栏的容器，物理文件的边界容器（如 common.py、header_processor.py 等），用以界定代码的组织单元。
### 3. 处理函数（Functions）
有蓝色顶栏的容器，是具体的业务执行单元，包含完整的 input 参数类型与 output 返回值类型契约。
### 4. 闲置/待重构函数（Unused Functions）
有红色高亮顶栏的容器，标识出当前尚未被下游引用的孤立节点（如 get_average_wideness），方便在重构时进行死代码清理或接入。
### 5. 调用边（Function Call / Invokes）
黑色以及蓝色箭头：同文件以及跨文件调用，体现单文件内部的自顶向下调用链和文件之间函数的调用关系，颜色不同仅为视觉区分。