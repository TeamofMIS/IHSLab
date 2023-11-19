---
title: CatRelDet
subtitle: 由奥地利克拉根福大学ITEC研究团队收集的白内障手术视频动作定位数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/CatRelDet.png
price: 开放获取
author: Ghamsarian et al.
date: 2021-01-10
rating: 4
features:
    -   label: 手术，眼科，视频，阶段
        icon: fa-file-archive
    -   label: CC BY-NC 4.0协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 杨宇轩维护
        icon: fa-id-card

---

CatRelDet数据集用于白内障手术视频动作定位，包含四个相关阶段的注释，每个相关阶段都提供了训练集和测试集。训练集中包含来自相关阶段的1000个三分钟视频片段，以及来自其他阶段的1000个三分钟视频片段。测试集中包含来自相关阶段的200个三分钟视频片段，以及来自其他阶段的200个三分钟视频片段。

相关阶段：

- Implantation 
- Irrigation_Aspiration Plus Viscoelastic_suction
- Phacoemulification 
- Rhexis 

数据集仅用于**科研目的**，不能用于商业或其他用途。使用时还需引用以下论文:

```bibtex
@inproceedings{DBLP:conf/icpr/GhamsarianTPSS20,
    author    = {Negin Ghamsarian and
                 Mario Taschwer and
                 Doris Putzgruber{-}Adamitsch and
                 Stephanie Sarny and
                 Klaus Schoeffmann},
    title     = {Relevance Detection in Cataract Surgery Videos by Spatio- Temporal
                 Action Localization},
    booktitle = {25th International Conference on Pattern Recognition, {ICPR} 2020,
                 Virtual Event / Milan, Italy, January 10-15, 2021},
    pages     = {10720--10727},
    publisher = {{IEEE}},
    year      = {2020},
    url       = {https://doi.org/10.1109/ICPR48806.2021.9412525},
    doi       = {10.1109/ICPR48806.2021.9412525},
    timestamp = {Fri, 07 May 2021 12:53:57 +0200},
    biburl    = {https://dblp.org/rec/conf/icpr/GhamsarianTPSS20.bib},
    bibsource = {dblp computer science bibliography, https://dblp.org}
}
```

### 访问地址

[CatRelDet](http://ftp.itec.aau.at/datasets/ovid/relevant-cat-actions/index.html)

### 关联数据集

- [cataract-21](/products/cataract-21)
- [cataract-101](/products/cataract-101)
- [IrisPupilSeg](/products/IrisPupilSeg)
- [CatInstSeg](/products/CatInstSeg)
- [CatRelComp](/products/CatRelComp)
- [LensID](/products/LensID)

### 相关论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Relevance Detection in Cataract Surgery Videos by Spatio- Temporal Action Localization | 2020 25th International Conference on Pattern Recognition (ICPR) | Ghamsarian et al. |  [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/abstract/document/9412525)  |  |
