

Anomaly-Detection

| features                                                | n_null | trait null value | description                                                  | 中文                               |
| ------------------------------------------------------- | ------ | ---------------- | ------------------------------------------------------------ | ---------------------------------- |
| index                                                   | 0      | -                | datetime                                                     |                                    |
| cell_id                                                 | 0      | -                |                                                              |                                    |
| DL_TRAFFIC_VOLUME                                       | 21     | drop             | DL => Download                                               |                                    |
| UL_TRAFFIC_VOLUME                                       | 21     | drop             | UP => Upload                                                 | 流量                               |
| Inter_X2_based_HO_prep                                  | 3313   | fill 0           | number of successful inter eNB X2 based HO prep) /. (total number of inter eNB X2 based. HO preparations) * 100%. |                                    |
| PDCP_SDU_Volume_DL                                      | 3313   | fill mean?       |                                                              |                                    |
| VoLTE_total_traffic                                     | 3313   | fill mean?       |                                                              |                                    |
| INTRA_FREQ_HO_SR_RATIO                                  | 40     | drop/mean        |                                                              |                                    |
| RRC_SR_RATIO                                            | 28     | drop             |                                                              |                                    |
| Intra_eNB_HO_SR_total_RATIO                             | 3321   | mean/            |                                                              |                                    |
| E_UTRAN_RRC_Conn_Stp_Failure_due_RRC_timer_expiry_RATIO | 3313   | mean/0 ？        |                                                              |                                    |
| CELL_AVAILABILITY_RATIO                                 | 20     | drop             |                                                              |                                    |
| RACH_Stp_Completion_SR_RATIO                            | 3313   |                  |                                                              |                                    |
| Total_E_UTRAN_RRC_Conn_Stp_SR_RATIO                     | 3313   |                  |                                                              |                                    |
| Inter_RAT_HO_SR_UTRAN_SRVCC_RATIO3                      | 5595   |                  |                                                              |                                    |
| UL_THROUGHPUT_RATIO                                     | 28     |                  |                                                              |                                    |
| E_RAB_QCI1_DR_RATIO                                     | 3324   |                  |                                                              |                                    |
| DCR_LTE_RATIO                                           | 28     |                  |                                                              |                                    |
| CSSR_LTE_RATIO                                          | 28     |                  |                                                              |                                    |
| LTE_INTER_ENODEB_HOSR_RATIO                             | 1695   |                  |                                                              |                                    |
| E_UTRAN_Inter_Freq_HO_SR_RATIO                          | 4967   |                  |                                                              |                                    |
| Inter_RAT_HO_SR_GERAN_SRVCC_RATIO                       | 3989   |                  |                                                              |                                    |
| Inter_RAT_Total_HO_SR_RATIO                             | 3731   |                  |                                                              |                                    |
| E_UTRAN_tot_HO_SR_inter_eNB_X2_RATIO                    | 3347   |                  |                                                              |                                    |
| DL_THROUGHPUT_RATIO                                     | 28     |                  |                                                              | 吞吐量比率： 实际吞吐量/预期吞吐量 |
| E_RAB_DR_RATIO                                          | 3314   |                  |                                                              |                                    |



E_RAB_DR  => E-Rab drop

HO => HandOver

SR => Success Rate





___

1695

- LTE_INTER_ENODEB_HOSR_RATIO  【这个如果是空值，下面几个也全是空值】

  

3313:   【这里面任意一个是空值，下面的其余几个也是空值】

- Inter_X2_based_HO_prep
- PDCP_SDU_Volume_DL
- VoLTE_total_traffic
- RACH_Stp_Completion_SR_RATIO
- **E_UTRAN_RRC_Conn_Stp**_Failure_due_RRC_timer_expiry_RATIO
- Total_**E_UTRAN_RRC_Conn_Stp**\_SR_RATIO

![image-20211119160716093](C:\Users\meiji\AppData\Roaming\Typora\typora-user-images\image-20211119160716093.png)





3314: 

- E_RAB_DR_RATIO

3321:

- Intra_eNB_HO_SR_total_RATIO

3324:

- E_RAB_QCI1_DR_RATIO   

3347:

- E_UTRAN_tot_HO_SR_inter_eNB_X2_RATIO

3731:

- Inter_RAT_Total_HO_SR_RATIO

3989:

- Inter_RAT_HO_SR_GERAN_SRVCC_RATIO

4967:

- E_UTRAN_Inter_Freq_HO_SR_RATIO  => 3313为空的时候有一个值不空

5595:

- Inter_RAT_HO_SR_UTRAN_SRVCC_RATIO


![image-20211119162041397](C:\Users\meiji\AppData\Roaming\Typora\typora-user-images\image-20211119162041397.png)



