---
title: Emotion-Gait
subtitle: 由美国马里兰大学GAMMA研究团队收集并标注的情绪步态数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/emotio-gait.png
price: 可直接获取
author: Uttaran Bhattacharya et al.
date: 2021-11-09
rating: 2
features:
    -   label: 情绪，步态，分类
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 陆璐维护
        icon: fa-id-card

---

Emotion-Gait数据集由2177个真实步态组成，每个步态由相同的10个注释者标记为四种情绪类别(愤怒，中性，快乐，悲伤)中的一种。具体来说，数据集由两部分组成:第一部分包含来自各种来源的342个步态，包括Human3.6M, ICT, CMU-MOCAP。第二部分包含来自ELMD的1835个步态。

### 访问地址

[Emotion-Gait](https://go.umd.edu/emotion-gait)

### 配置方式

# Emotion-Gait
Our new dataset consisting of human gaits annotated with 4 emotion labels: angry, happy, neutral and sad.

It consists of 2,177 real gaits and 4,000 synthetic gaits.

Of the 2,177 real gaits, 342 were collected by us and the remaining 1,835 were taken from the [Edinburgh Locomotion Mocap Database (ELMD)](https://bitbucket.org/jonathan-schwarz/edinburgh_locomotion_mocap_dataset/src/master/) and annotated by us. Please also cite the paper in the ELMD download page if using this dataset.


All 342 real gaits we collected are stored in the file features.h5.
All the 1,835 gaits taken from ELMD are stored in the file features_ELMD.h5.

The format for each data file is T x V, where T is the number of 
time steps and V is the number of coordinate locations.
T varies from file to file, V is fixed for all the files. 
Specifically, each row of length V consists of the following entries in the given order:

<root joint x> <root joint y> <root joint z> <spine joint x> <spine joint y> <spine joint z>
<neck joint x> <neck joint y> <neck joint z>
<head joint x> <head joint y> <head joint z>
<left shoulder joint x> <left shoulder joint y> <left shoulder joint z>
<left elbow joint x> <left elbow joint y> <left elbow joint z>
<left hand joint x> <left hand joint y> <left hand joint z>
<right shoulder joint x> <right shoulder joint y> <right shoulder joint z>
<right elbow joint x> <right elbow joint y> <right elbow joint z>
<right hand joint x> <right hand joint y> <right hand joint z>
<left hip joint x> <left hip joint y> <left hip joint z>
<left knee joint x> <left knee joint y> <left knee joint z>
<left foot joint x> <left foot joint y> <left foot joint z>
<right hip joint x> <right hip joint y> <right hip joint z>
<right knee joint x> <right knee joint y> <right knee joint z>
<right foot joint x> <right foot joint y> <right foot joint z>.



The corresponding label for each data file in features.h5 is stored in labels.h5.
The corresponding label for each data file in features_ELMD.h5 is stored in labels_ELMD.h5.

The original multi-class labels for the ELMD dataset, provided by our ten annotators, is available in labels_edin_locomotion.zip. We have used these labels in our work "Take an Emotion Walk".


All synthetic gaits are in two parts in the two files features_CVAEGCN_1_2000.h5 and features_CVAEGCN_2001_4000.h5.
The format of storing gaits is same as the format for features.h5. Moreover, each data file in the synthetic gait is named as
<gait ID><label>.
e.g., the happy gait with ID 3 is stored as 00003_Happy. Thus, the synthetic gaits are self labeled.


### Benchmarks

| 任务                         | 数据集变体         | 最优模型        | 代码                                                     | 论文                                                                |
|----------------------------|---------------|-------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Gait emotion recognition       | Emotion-Gait      | STEP     | [<i class="fa-brands fa-github"/>](https://github.com/UttaranB127/STEP) | [<i class="fa-solid fa-file"/>](https://obj.umiacs.umd.edu/gamma-umd-website-imgs/pdfs/affectivecomputing/STEP.pdf) |



### 小组论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|

