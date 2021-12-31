[TOC]



## 1. Data preprocessing

### 1.1 Data understanding

In this project, we begin with a small sample dataset, which has only 8280 records.

<img src="images/image-20211210103241230.png" alt="image-20211210103241230" style="zoom: 50%;" />

In this data 26 features are measured, including time, cell id and some KPIs of LTE (Long Term Evolution, a wireless data communication technology standard).

These data come from 5 cells, that is, each cell contains 1656 records.

> `cell` : In the field of mobile communication, the area covered by wireless signals is called a *cell*, which generally refers to the area that can be covered by the signal of a base station.



First Let's check the NaN values in our dataset :

```
index                                                         0
cell_id                                                       0
DL_TRAFFIC_VOLUME                                            21
UL_TRAFFIC_VOLUME                                            21
Inter_X2_based_HO_prep                                     3313
PDCP_SDU_Volume_DL                                         3313
VoLTE_total_traffic                                        3313
INTRA_FREQ_HO_SR_RATIO                                       40
RRC_SR_RATIO                                                 28
Intra_eNB_HO_SR_total_RATIO                                3321
E_UTRAN_RRC_Conn_Stp_Failure_due_RRC_timer_expiry_RATIO    3313
CELL_AVAILABILITY_RATIO                                      20
RACH_Stp_Completion_SR_RATIO                               3313
Total_E_UTRAN_RRC_Conn_Stp_SR_RATIO                        3313
Inter_RAT_HO_SR_UTRAN_SRVCC_RATIO                          5595
UL_THROUGHPUT_RATIO                                          28
E_RAB_QCI1_DR_RATIO                                        3324
DCR_LTE_RATIO                                                28
CSSR_LTE_RATIO                                               28
LTE_INTER_ENODEB_HOSR_RATIO                                1695
E_UTRAN_Inter_Freq_HO_SR_RATIO                             4967
Inter_RAT_HO_SR_GERAN_SRVCC_RATIO                          3989
Inter_RAT_Total_HO_SR_RATIO                                3731
E_UTRAN_tot_HO_SR_inter_eNB_X2_RATIO                       3347
DL_THROUGHPUT_RATIO                                          28
E_RAB_DR_RATIO                                             3314
```

In the data, many variables have a large number of missing values! Usually, for missing values, we will either fill them using other means or delete features/records with many missing values. In our case, it can be seen that there is perhaps some correlation between these missing values (e.g., many variables have 3313 missing values). We will start with one point and then move on to consider how to deal with them.

After a brief glance at the data file in Excel, we found this:

<img src="images/in_excel.jpg" style="zoom:40%;" />

Apparently, these missing values are concentrated in a certain region. More specifically, for this cell, it seems that it does not contain certain KPIs. In the next step, we can consider processing the data of different cells separately.

> Viewing data in Excel is a very simple way (so simple that it is sometimes overlooked) to provide some information in a very visual way



Then we'll look into the data distribution : 

<img src="images/data distribution.png" style="zoom:50%;" />

It can be seen that the data corresponding to the features are all continuous. For some of the features related to "ratio", the data set is distributed around 1 or 0 (in the above figure it looks like a binary distribution, but it has actually continuous values). 

Therefore, we can later use some missing value padding methods for continuous numeric variables.



### 1.2 Look into data by `cell_id`

#### (1) Features with too many missing values

Based on the above discussion, we looked at the missing values in each cell data

![](images/NaN_count_by_cell.png)

It is obvious that for the third and fourth cell in the above figure, they have much more missing values on certain features.  To see it more clearly, we can print out only those who do not have too many NaN values (less than 30% for example).

![](images/after_drop.png)



Now, we will look into the distribution of those columns whose NaN values need to be filled later.

<img src="images/distribution_of_sparse_columns.png" style="zoom:60%;" />

For different distributions, we can propose the following strategies:

- fill with <u>mode</u> : for some features with very concentrated distribution (e.g. `Inter_RAT_HO_SR_GERAN_SRVCC_RATIO`, row3, col1 in figure above)

- fill with <u>mean</u> : for features with relatively large variance (e.g. `VoLTE_total_traffic`, row1, col2 in figure above)

- fill with <u>specific value</u> depending on the definition of feature : the data distribution of certain features varies widely across cells (e.g. `E_UTRAN_Inter_Freq_HO_SR_RATIO`, row3, col3 in figure above. Its left and right parts are actually the distribution in two different cells).

  In our case, the <u>median</u> is used. This is because it is tested that the median of these characteristics is very close to the center of one of the distributions. Of course, we can be very flexible in our approach for this type of features



#### (2) Highly relevant features

When modeling, features that are highly correlated can cause redundancy. We can simplify the model by keeping only the features with weak correlation.

![](images/correlation.png)

Since our data itself is not particularly high dimensional and the correlation between the features presented in each cell is different, we need to be careful when removing them. To do this, we can record the features that are identified as redundant in each cell, and then remove those that are redundant for most cells (4 cells out of 5, in our case), and there are 6 features meet the criteria to delete.



### 1.3 Fill NaN & Add new features

According to the above analysis, we first remove the redundant feature values and then fill them according to different methods

On top of the above, we will also consider the effects of time of day ( hour of a day) and cell.

Finally, we get 8279 data containing 20 features.



## 2. Modeling

### 2.1 Isolation Forest

#### (1) Introduction

The isolation forest algorithm is proposed for detecting outliers. Regarding the anomalies, they are considered to have the following two characteristics:

- they are the minority consisting of fewer instances
- they have attribute-values that are very different from those of normal instance

In brief, the solution idea proposed by the isolation forest is as follows:

1. Randomly select a feature and its segmentation value
2. Recursively segment the dataset only until it is indivisible/attains maximum depth

In general, the more easily the point is to be isolated the more likely it would be an anomaly.

<img src="images/image-20211228215515386.png" alt="Separation(x0 is more likely to be an outlier than xi)" style="zoom: 80%;" />

#### (2) Implementation

Since there is already a wrapped isolated forest algorithm in python's `sklearn` library, we can use it directly.  Here, we use the default parameters:

```python
from sklearn.ensemble import IsolationForest

IF=IsolationForest(n_estimators=150, 
                      max_samples ='auto', 
                      max_features=1,random_state=42)
```

> Note: For this algorithm, we do not need to scale the features

After the model is trained, we use it to calculate the anomaly score for each sample (the higher the score, the more likely it is to be an outlier)



### 2.2 Variational Auto-Encoder (VAE)

#### (1) Introduction

Before getting into VAE, let's get to see its simplified version first - the *AutoEncoder* (AE).

**AutoEncoder** is based on neural networks. Its core idea is to take the features of the learned data and thus reduce the dimensionality (usually in a non-linear way), and this process is called ***encoding***. And to retrieve them from the "compressed" data, we need to perform the ***decoding*** process. Of course, the decompressed data will not be exactly the same as the original one. Therefore, the main purpose of training the model is to reduce the discrepancy between the two.

<img src="https://img-blog.csdnimg.cn/20190625194923365.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM2MTk3OTEz,size_16,color_FFFFFF,t_70" alt="AE" style="zoom:35%;" />

The autoencoder is a single-valued mapping model (compressing a sample point into another point and then retrieving it), while the variational autoencoder looks for the mapping relationship of the distribution. Specifically, the VAE learns their distribution from the "compressed points", so that any point in this "compressed space" can find its counterpart in the original space.

<img src="https://img-blog.csdnimg.cn/20190625195051525.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM2MTk3OTEz,size_16,color_FFFFFF,t_70" alt="VAE" style="zoom:40%;" />

Based on the above features, we can apply AE to detect outliers and use VAE to generate new data that is not available in reality. Although our aim is to detect anomalies, given the other requirements of this project, we will also use the VAE model.

#### (2) Implementation

As before, we start with a relatively simple model structure. Specifically, in the encoder and decoder, we just use two dense layers:

<img src="images/image-20211231014407279.png" alt="image-20211231014407279" style="zoom:67%;" />

<img src="images/image-20211231014427847.png" alt="image-20211231014427847" style="zoom:67%;" />

For the VAE model, we need to calculate the difference (which we call "loss") between the reconstructed data and the original data, and outliers tend to have larger losses. 

### 2.3 Combination of *IF* and *VAE*

Combining the above theory, we can easily think that the output of the above encoder can be used as input to the *IF* model. For this, we can interpret it as using the new features learned by VAE as input (they may be more representative than the original ones) and then isolating those points that are far from the majority with *IF* algorithm. In this step, we keep the basic settings of the two models above.



## 3. Evaluation 

Next, we will analyze the output metrics (abnormal probabilities or losses) of the three models mentioned above.

### 3.1 Analysis on metrics

#### (1) Distribution

|               | IF_score                                                     | VAE_loss                                                     | VAE_IF_score                                                 |
| ------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| mean          | 0.451213                                                     | 0.747234                                                     | 0.486291                                                     |
| std           | 0.030114                                                     | 2.107643                                                     | 0.039233                                                     |
| min           | 0.403969                                                     | 0.023347                                                     | 0.423997                                                     |
| 25%           | 0.428277                                                     | 0.194180                                                     | 0.458176                                                     |
| 50%           | 0.443766                                                     | 0.377740                                                     | 0.474988                                                     |
| 75%           | 0.471093                                                     | 0.776217                                                     | 0.508637                                                     |
| max           | 0.599004                                                     | 97.686493                                                    | 0.715104                                                     |
| visualization | <img src="images/image-20211231020504776.png" alt="image-20211231020504776" style="zoom: 50%;" /> | <img src="images/image-20211231020518046.png" alt="image-20211231020518046" style="zoom:50%;" /> | <img src="images/image-20211231020530818.png" alt="image-20211231020530818" style="zoom:50%;" /> |

> Note: In the above visualization a box line plot is used, which uses a method called ***IQR*** (which we will explain in detail later).
>
> - orange line represents the median
> - the upper and lower boundaries of the rectangle represent the upper(Q3) and lower quartiles(Q1)
> - the two line segments outside of it represent the upper and lower boundaries
> - circles beyond the boundaries are considered as outliers.



It can be seen that the two algorithms related to isolated forest get similar results, while in *VAE_loss* there is a large difference in its value because the value of loss is taken without an upper limit. 

To make it clear, we can use the IQR method to calculate the upper bound and its corresponding quantile and get the following results:

|                        | IF_score | VAE_loss | VAE_IF_score |
| ---------------------- | -------- | -------- | ------------ |
| upper bound            | 0.54     | 1.65     | 0.58         |
| Corresponding Quantile | 98.90%   | 91.94%   | 97.26%       |
| Anomaly percentage     | 1.1%     | 8.06%    | 2.74%        |

#### (2) Interrelation

Next, we put the three metrics together to observe their interrelationship. Since the value range of *VAE_loss* differs significantly from the other two, we use color to indicate it (the darker the color, the larger the value)

<img src="images/image-20211231022646570.png" alt="image-20211231022646570" style="zoom:67%;" />

It can be seen that：
1. In the lower left corner of the image ( x∈[0, 0.45] and y∈[0, 0.55] )
    the values of all three indicators are low, which means that it is less likely to be an outlier
2. In the upper part of the image (y>0.57) as well as in the right part (x>0.51), when both `IF_score` and `VAE_IF_score` have higher scores, `VAE_loss` also tends to have higher values (i.e., all three metrics are considered more likely to be anomalous)
3. x∈[0.46, 0.51] and y∈[0.47, 0.56] 
    `VAE_IF_loss` indicates high loss, but for the other two indicators, the same conclusion is not always reached

#### (3) Visualize metrics on original data

Since our original data is 20-dimensional, in order to visualize them, we can use PCA to downscale it to 2~3 dimensions. Of course, reducing the dimensionality will introduce some information loss, but we are here mainly to facilitate the demonstration of the results.



|      | IF_score                                                     | VAE_loss                                                     | VAE_IF_score                                                 |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2D   | <img src="images/image-20211231025928407.png" alt="image-20211231025928407" style="zoom:50%;" /> | <img src="images/image-20211231030031250.png" alt="image-20211231030031250" style="zoom:50%;" /> | <img src="images/image-20211231030158603.png" alt="image-20211231030158603" style="zoom:50%;" /> |
| 3D   | <img src="images/image-20211231025803418.png" alt="image-20211231025803418"  /> | <img src="images/image-20211231025949531.png" alt="image-20211231025949531"  /> | ![image-20211231030142496](images/image-20211231030142496.png) |

In the above figure, we compressed the original 20-dimensional data to 2~3 dimensions respectively for visualization. Where the darker color indicates the higher possibility of anomaly. It can be seen that.

- **Isolation Forest** is good at finding out the data edges as well as very small clusters.

- **VAE** is very sensitive to clustering who is far away from majority

- **VAE+IF**: the difference in values is relatively small, but we can still find some points at the edges where the possibility of anomalies is high

  

### 3.2 Identification of anomalies

#### (1) IQR method

In descriptive statistics, the interquartile range (IQR) is **a measure of statistical dispersion**, which is the spread of the data. It is defined as the difference between the 75th and 25th percentiles of the data. 

Mathematically, we have:

- IQR = Q3 - Q1
- upper_bound = Q3 + 1.5*IQR
- lower_bound  = Q1 - 1.5*IQR

where Q1, Q3 are 25% and 75% quantile respectively. But in our case, the lower bound won't be used, since the lower the metric value is, the less possible a record will be an anomaly.

For each data record, we compare whether its value for each metric exceeds the above upper bound. By using the IQR method, we can determine if the record has an exception based on how many metrics indicate it's an anomaly. If we consider a record is abnormal when `n` metric(s) indicate that, then we have : 
- `n` = 1,  the anomaly ratio is 9.30%
- `n` = 2,  the anomaly ratio is 2.35%
- `n` = 3,  the anomaly ratio is 0.24%

<img src="images/image-20211231025403519.png" alt="image-20211231025403519" style="zoom: 67%;" />

#### (2) Stacking

If we use the idea of stacking in an integrated model (making the output of some model the input of another model), we can choose to use these three metrics as the input of another IF model.

We use the default parameters and the rest of the steps are similar to what we mentioned in the previous section on models. After visualizing the results, we get the following:

<img src="images/image-20211231025335558.png" alt="image-20211231025335558" style="zoom:67%;" />
