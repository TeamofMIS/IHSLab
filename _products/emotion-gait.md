---
title: Emotion-Gait
subtitle: 由美国马里兰大学GAMMA研究团队收集并标注的情绪步态数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/emotio-gait.png
price: 开放获取
author: Uttaran Bhattacharya et al.
date: 2021-11-09
rating: 2
features:
    -   label: 情绪，步态，分类
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: 【Big-data节点】？
        icon: fa-desktop
    -   label: 陆璐维护
        icon: fa-id-card

---

Emotion-Gait数据集由2177个真实步态组成，每个步态由相同的10个注释者标记为四种情绪类别(愤怒，中性，快乐，悲伤)中的一种。具体来说，数据集由两部分组成:第一部分包含来自各种来源的342个步态，包括Human3.6M, ICT, CMU-MOCAP。第二部分包含来自ELMD的1835个步态。

### 访问地址

[Emotion-Gait](https://go.umd.edu/emotion-gait)

### 配置方式

Emotion-Gait数据集由人类步态组成，并标注了4种情绪标签：愤怒、快乐、中性和悲伤。

该数据集由2,177个真实步态和4,000个合成步态组成。

在2,177个真实步态中，342个由我们收集，其余1,835个来自 [Edinburgh Locomotion Mocap Database (ELMD)](https://bitbucket.org/jonathan-schwarz/edinburgh_locomotion_mocap_dataset/src/master/) 并由我们注释。如果使用该数据集，请同时引用ELMD下载页面中的论文。


我们收集的所有342个真实步态都存储在features.h5文件中。
从ELMD中提取的全部1,835个步态存储在features_ELMD.h5文件中。

每个数据文件的格式为T x V，其中 T 是时间步数，V是时间步数。
每个文件的T都不同，而所有文件的V都是固定的。
具体来说，长度为 V 的每一行都按给定顺序由以下条目组成：
```xml
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
```

features.h5中每个数据文件的相应标签存储在labels.h5中。
features_ELMD.h5中每个数据文件的相应标签存储在labels_ELMD.h5中。

由十位注释者提供的ELMD数据集的原始多类标签可在labels_edin_locomotion.zip中找到。我们在作品 "Take an Emotion Walk "中使用了这些标签。
所有合成步态被划分为两个部分features_CVAEGCN_1_2000.h5和features_CVAEGCN_2001_4000.h5。
步态的存储格式与features.h5的格式相同。合成步态中的每个数据文件命名为<步态ID> <标签> 。



### Benchmarks

| 任务                         | 数据集变体         | 最优模型        | 代码                                                     | 论文                                                                |
|----------------------------|---------------|-------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Step: Spatial temporal graph convolutional networks for emotion perception from gaits | Emotion-Gait      | STEP     | [<i class="fa-brands fa-github"/>](https://github.com/UttaranB127/STEP) | [<i class="fa-solid fa-file"/>](https://obj.umiacs.umd.edu/gamma-umd-website-imgs/pdfs/affectivecomputing/STEP.pdf) |
| Tntc: two-stream network with transformer-based complementarity for gait-based emotion recognition | Emotion-Gait      | Tntc     |  | [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=9746047) |
| EPIC: Emotion Perception by Spatio-Temporal Interaction Context of Gait | Emotion-Gait      | EPIC     |  | [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=10005028) |
