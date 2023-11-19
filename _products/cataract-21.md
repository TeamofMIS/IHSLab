---
title: Cataract-21
subtitle: 由奥地利克拉根福大学ITEC研究团队收集的白内障手术视频数据集 
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/cataract-21.jpg
price: 开放获取
author: Primus et al.
date: 2018-01-13
rating: 3
features:
    - label: 手术,眼科,视频,阶段
      icon: fa-file-archive 
    - label: CC BY-NC-SA 4.0协议,非商业可用
      icon: fa-copyright
    - label: Big-data节点
      icon: fa-desktop
    - label: 杨宇轩维护
      icon: fa-id-card
---

白内障手术是眼科领域最常进行的显微手术之一，这种手术的目的是用人造晶状体取代人眼晶状体。整个手术在显微镜下进行，摄像头可以记录和存档整个过程。Cataract-21数据集包含21个白内障手术视频，共分为训练集(17个)和验证集(4个)。每个视频都有相应的CSV注释文件，将每帧映射到10个手术阶段类别之一，由奥地利Klinikum Klagenfurt医院的专家注释。其中，不属于任何阶段的视频片段都打上了“not_initialized”的标签(特别是在第一个阶段“Incision”之前的部分)。

阶段定义如下：

| Classnumber | Phase                 | Label                      |
|-------------|-----------------------|----------------------------|
| 1           | Phase 1               | Incision                   |
| 2           | Phase 2 and Phase 8   | Viscous agent injection    |
| 3           | Phase 3               | Rhexis                     |
| 4           | Phase 4               | Hydrodissection            |
| 5           | Phase 5               | Phacoemulsification        |
| 6           | Phase 6               | Irrigation and aspiration  |
| 7           | Phase 7               | Capsule polishing          |
| 8           | Phase 9               | Lens implant setting-up    |
| 9           | Phase 10              | Viscous agent removal      |
| 10          | Phase 11              | Tonifying and antibiotics  |

数据集仅用于**科研目的**，不能用于商业或其他用途。使用时还需引用以下论文:

```bibtex
@inproceedings{DBLP:conf/mmm/PrimusPTMEBS18,
  author    = {Manfred J{\"{u}}rgen Primus and
               Doris Putzgruber{-}Adamitsch and
               Mario Taschwer and
               Bernd M{\"{u}}nzer and
               Yosuf El{-}Shabrawi and
               L{\'{a}}szl{\'{o}} B{\"{o}}sz{\"{o}}rm{\'{e}}nyi and
               Klaus Schoeffmann},
  editor    = {Klaus Schoeffmann and
               Thanarat H. Chalidabhongse and
               Chong{-}Wah Ngo and
               Supavadee Aramvith and
               Noel E. O'Connor and
               Yo{-}Sung Ho and
               Moncef Gabbouj and
               Ahmed Elgammal},
  title     = {Frame-Based Classification of Operation Phases in Cataract Surgery
               Videos},
  booktitle = {MultiMedia Modeling - 24th International Conference, {MMM} 2018, Bangkok,
               Thailand, February 5-7, 2018, Proceedings, Part {I}},
  series    = {Lecture Notes in Computer Science},
  volume    = {10704},
  pages     = {241--253},
  publisher = {Springer},
  year      = {2018},
  url       = {https://doi.org/10.1007/978-3-319-73603-7\_20},
  doi       = {10.1007/978-3-319-73603-7\_20},
  timestamp = {Tue, 14 May 2019 10:00:42 +0200},
  biburl    = {https://dblp.org/rec/conf/mmm/PrimusPTMEBS18.bib},
  bibsource = {dblp computer science bibliography, https://dblp.org}
}
```

### 访问地址

[Cataract-21](http://ftp.itec.aau.at/datasets/ovid/cat-21/) 

### 关联数据集

- [cataract-101](/products/cataract-101)
- [IrisPupilSeg](/products/IrisPupilSeg)
- [CatInstSeg](/products/CatInstSeg)
- [CatRelComp](/products/CatRelComp)
- [CatRelDet](/products/CatRelDet)
- [LensID](/products/LensID)

### 相关论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Frame-Based Classification of Operation Phases in Cataract Surgery Videos | International Conference on Multimedia Modeling (MMM 2018) | Primus et al. |  [<i class="fa-solid fa-file"/>](https://link.springer.com/chapter/10.1007/978-3-319-73603-7_20)  |  |