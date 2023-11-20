---
title: DEAP
subtitle: 基于生理信号的情绪分析数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/DEAP.jpg
price: 开放获取
author: S. Koelstra et al.
date: 2011-06-09
rating: 4
features:
    -   label: 非接触式，心率检测，情感识别
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 秦玉婷维护
        icon: fa-id-card

---

DEAP为情感计算领域的开源数据集，可用于非接触式心率检测。数据集包括问卷评分、中枢和外周生理信号、面部表情等多源数据。采集过程中安排32位受试者在线观看并评价40个音乐视频，同步记录受试者生理信号和部面部表情视频（720×576@50fps），观看结束后受试者在唤醒度、效价、支配度、喜欢度和熟悉度五个维度上进行打分标注。生理信信号包括ECG信号、PPG信号、呼吸信号、皮肤电GSR、皮肤温度、肌电图、眼电图。

### 访问地址

[DEAP](https://www.eecs.qmul.ac.uk/mmv/datasets/deap/index.html)

### 配置方式

1.PPG标签处理

```python
datapath = r'/home/som/lab/seed-yzj/DEAP/data/deap_ppg.csv'
savepath = r'/home/som/lab/seed-yzj/DEAP/data/new_deap_ppg.csv'
write = open(savepath, 'a', newline='', encoding='gb18030')
writer = csv.writer(write)

with open(datapath, 'r', encoding="utf-8") as csvfile:
reader = csv.reader(csvfile)

for row in reader:
    if len(row[0].split('_')[-1])==2:
        newnum=row[0].split('_')[0]+'_trial'+row[0].split('_')[-1]
    else:
        newnum = row[0].split('_')[0] + '_trial0' + row[0].split('_')[-1]

    ppg=[float(i) for i in row[sr*5+1:sr*25+1]]

    detrend_signals = detrend(ppg, 100)
    detrend_signals = detrend_signals.flatten()

    normalized_signals = normalize(detrend_signals)
    resample_signal=signal.resample(normalized_signals,50*20)

    recording =[]
    recording.append(newnum)
    for j in range(len(resample_signal)):
        recording.append(resample_signal[j])

    writer.writerow(recording)

def detrend(signals,param_lambda):
        # https://blog.csdn.net/piaoxuezhong/article/details/79211586
    signal_len = len(signals)
    I = np.identity(signal_len)
    B = np.array([1, -2, 1])
    ones = np.ones((signal_len - 2, 1))
    multi = B * ones
    D2 = np.zeros((signal_len - 2, signal_len))
    for i in range(D2.shape[0]):
        D2[i, i:i + 3] = multi[i]
    tr_D2 = np.transpose(D2)
    multi_D2 = np.dot(tr_D2, D2)
    inverse = I - (np.linalg.inv(I + (multi_D2 * pow(param_lambda, 2))))
    detrend_signals = np.dot(inverse, signals)
    return detrend_signals

def normalize(signals):
    normalized_signals = (signals - np.mean(signals)) / np.std(signals, ddof=1)
    return normalized_signals
```

### 组内论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Deep Super-Resolution Network for rPPG Information Recovery and Noncontact Heart Rate Estimation | IEEE Transactions on Instrumentation and Measurement | Zijie Yue, Shuai Ding, Shanlin Yang, Hui Yang, Zhili Li, Youtao Zhang, Yinghui Li |  [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/abstract/document/9529062)  |  |