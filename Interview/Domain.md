# CRM过程和数据领域(元数据、表单配置、数据可视化)
## 概念

## 特点
1. 业务共性和差异性，体现为销售属性(BU、组织、兵种)、门店属性(区域、品类、阶段)、业务(MEM、CPC、交易)之间排列组合产生的差异性。
2. 业务内容、规则、流程、页面展现差异。

## 挑战
1. 业务隔离和复用
2. 内容、规则、流程、页面等差异性

## 方案
1. 技术架构：水平分层(元数据层、交互协议层、投放层);垂直分治(简单规则和流程编排)
2. 低代码可视化配置方案(表单引擎、页面搭建引擎、图表可视化)

# 电商营销链路(交易链路卡券红包积分、营销活动、互动任务)

## 概念

## 特点
1. 大促，高并发量
2. 业务形态多变
3. 用户体验和资金风险

## 挑战
1. 高并发
2. 业务复杂度(形态多变、快速交付)
3. 高可用和资损防控

## 方案
1. 业务架构：权益(卡券分金)、活动(抽发领返)、任务(主动&事件&定时)
2. 技术架构：水平分层(权益层、活动层、任务层)；垂直分治(积分、库存、运营平台、调度中心、投放平台、时空穿越平台、事件计算中心、关系平台)。
3. 核心技术点：权益&活动&任务模型接口标准化，权益接入代理(SPI)，活动渲染和发放流程编排，活动分发缓存机制，热点库存扣减，权益匹配算法实现，
   多任务组合计算体系
   
# IM和社交领域(IM连接层、业务层、协议层、客户端)

## 概念

## 特点

## 挑战
1. 高并发
2. 海量数据
3. 高可用(偏持续服务)


## 方案
