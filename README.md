# Anomaly-Detection

## 1. 特征值解读

LTE KPIs

|      | features                                                | n_null |
| ---- | ------------------------------------------------------- | ------ |
|      | index                                                   | 0      |
|      | cell_id                                                 | 0      |
| 1    | DL_TRAFFIC_VOLUME                                       | 21     |
| 2    | UL_TRAFFIC_VOLUME                                       | 21     |
| 3    | Inter_X2_based_HO_prep                                  | 3313   |
| 4    | PDCP_SDU_Volume_DL                                      | 3313   |
| 5    | VoLTE_total_traffic                                     | 3313   |
| 6    | INTRA_FREQ_HO_SR_RATIO                                  | 40     |
| 7    | RRC_SR_RATIO                                            | 28     |
| 8    | Intra_eNB_HO_SR_total_RATIO                             | 3321   |
| 9    | E_UTRAN_RRC_Conn_Stp_Failure_due_RRC_timer_expiry_RATIO | 3313   |
| 10   | CELL_AVAILABILITY_RATIO                                 | 20     |
| 11   | RACH_Stp_Completion_SR_RATIO                            | 3313   |
| 12   | Total_E_UTRAN_RRC_Conn_Stp_SR_RATIO                     | 3313   |
| 13   | Inter_RAT_HO_SR_UTRAN_SRVCC_RATIO                       | 5595   |
| 14   | UL_THROUGHPUT_RATIO                                     | 28     |
| 15   | E_RAB_QCI1_DR_RATIO                                     | 3324   |
| 16   | DCR_LTE_RATIO                                           | 28     |
| 17   | CSSR_LTE_RATIO                                          | 28     |
| 18   | LTE_INTER_ENODEB_HOSR_RATIO                             | 1695   |
| 19   | E_UTRAN_Inter_Freq_HO_SR_RATIO                          | 4967   |
| 20   | Inter_RAT_HO_SR_GERAN_SRVCC_RATIO                       | 3989   |
| 21   | Inter_RAT_Total_HO_SR_RATIO                             | 3731   |
| 22   | E_UTRAN_tot_HO_SR_inter_eNB_X2_RATIO                    | 3347   |
| 23   | DL_THROUGHPUT_RATIO                                     | 28     |
| 24   | E_RAB_DR_RATIO                                          | 3314   |



**abbr:**

DR  =>  Drop Rate

HO => Hand Over

SR => Success Rate

Stp => Set up

eNB = > eNodeB



**notions:**

UTRAN：3G的无线接入

E-UTRAN：4G的无线接入网

RRC: RRC连接首先包括SRB1的建立。E-UTRAN在完成S1连接建立过程前，也就是在接收EPC发出的UE上下文信息之前，完成RRC连接的建立。因此，在RRC连接的初始阶段，并不会激活AS安全，E-UTRAN可以配置UE进行测量上报，但UE只在安全激活后才接收切换信息

E-RAB：用于Uefa和CN之间传送语音、数据及多媒体业务的用户平面承载

Inter-RAT HandOver： 从一个E-UTRAN到另一个RAT

SRVCC（Single Radio Voice Call Continuity）是3GPP提出的一种VoLTE语音业务连续性方案

GERAN（GSM EDGE Radio Access Network）是 GSM/EDGE 无线通讯网络







## 2. KPI归类

### **UL & DL**

流量

- [01] DL_TRAFFIC_VOLUME
- [02] UL_TRAFFIC_VOLUME
- [05] VoLTE_total_traffic

吞吐量

- [23] DL_THROUGHPUT_RATIO
- [14] UL_THROUGHPUT_RATIO



### Inter-RAT

- [13] Inter_RAT_HO_SR_UTRAN_SRVCC_RATIO
- [20] Inter_RAT_HO_SR_GERAN_SRVCC_RATIO
- [21] Inter_RAT_Total_HO_SR_RATIO



### E-UTRAN

- [09] E_UTRAN_RRC_Conn_Stp_Failure_due_RRC_timer_expiry_RATIO 
- [12] Total_E_UTRAN_RRC_Conn_Stp_SR_RATIO
- [19] E_UTRAN_Inter_Freq_HO_SR_RATIO
- [22] E_UTRAN_tot_HO_SR_inter_eNB_X2_RATIO



- [07] RRC_SR_RATIO



RRC连接首先包括SRB1的建立。

E-UTRAN在完成S1连接建立过程前，完成RRC连接的建立。因此，在RRC连接的初始阶段，并不会激活AS安全，E-UTRAN可以配置UE进行测量上报，但UE只在安全激活后才接收切换信息



### E-RAB

- [15] E_RAB_QCI1_DR_RATIO
- [24] E_RAB_DR_RATIO



### LTE

- [16] DCR_LTE_RATIO

- [17] CSSR_LTE_RATIO

  

### E-NB/ENodeB

- [18] LTE_INTER_ENODEB_HOSR_RATIO
- [08] Intra_eNB_HO_SR_total_RATIO



### 其他

- [03] Inter_X2_based_HO_prep
- [04] PDCP_SDU_Volume_DL
- [06] INTRA_FREQ_HO_SR_RATIO
- [10] CELL_AVAILABILITY_RATIO
- [11] RACH_Stp_Completion_SR_RATIO







## 备忘

**IF**：

- 分析特征之间相关性，选取相关性弱的特征

- 重新优化填充方法

- 使用分数而不是分类

  

把三个方法的分数合并起来 然后再判断







# References:

 [处理 Python 中的缺失值——完整指南](https://www.analyticsvidhya.com/blog/2021/05/dealing-with-missing-values-in-python-a-complete-guide/)

插值法([df.interpolate](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.interpolate.html))：[插值 - Python 中的插值功能可以填补缺失值](https://www.analyticsvidhya.com/blog/2021/06/power-of-interpolation-in-python-to-fill-missing-values/)

[内插法和外推法简介](https://www.cnblogs.com/luluathena/archive/2010/11/27/1889906.html)



[隔离森林进行异常检测的python代码示例 - 有监督](https://practicaldatascience.co.uk/machine-learning/how-to-use-the-isolation-forest-model-for-outlier-detection)

[无监督Isolation Forest异常检测](https://www.kaggle.com/rgaddati/unsupervised-fraud-detection-isolation-forest)



[Nicolas Goix：如何评估无监督AD算法](https://ngoix.github.io/slides_icml2016.pdf)，[论文版](https://arxiv.org/pdf/1607.01152.pdf)
