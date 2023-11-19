---
title: LensID
subtitle: 由奥地利克拉根福大学ITEC研究团队收集的白内障手术视频数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/LensID.png
price: 开放获取
author: Ghamsarian et al.
date: 2021-09-21
rating: 4
features:
    -   label: 手术，眼科，视频，阶段，分割
        icon: fa-file-archive
    -   label: CC BY-NC 4.0协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 杨宇轩维护
        icon: fa-id-card

---

LensID数据集包含用于白内障手术视频中镜片检测研究的视频片段、镜片分割和瞳孔分割。数据集包含3个部分:

1. 100个白内障手术视频的镜片植入阶段识别,包含2040个训练视频片段和360个测试视频片段。

2. 401帧图像的镜片分割,包含292帧训练图像和109帧测试图像。 

3. 189帧图像的瞳孔分割,包含141帧训练图像和48帧测试图像。


数据集仅用于**科研目的**，不能用于商业或其他用途。使用时还需引用以下论文:

```bibtex
@inproceedings{DBLP:conf/miccai/GhamsarianTPSES21,
    author    = {Negin Ghamsarian and
                 Mario Taschwer and
                 Doris Putzgruber{-}Adamitsch and
                 Stephanie Sarny and
                 Yosuf El{-}Shabrawi and
                 Klaus Schoeffmann},
    editor    = {Marleen de Bruijne and
                 Philippe C. Cattin and
                 St{\'{e}}phane Cotin and
                 Nicolas Padoy and
                 Stefanie Speidel and
                 Yefeng Zheng and
                 Caroline Essert},
    title     = {LensID: {A} CNN-RNN-Based Framework Towards Lens Irregularity Detection
                 in Cataract Surgery Videos},
    booktitle = {Medical Image Computing and Computer Assisted Intervention - {MICCAI}
                 2021 - 24th International Conference, Strasbourg, France, September
                 27 - October 1, 2021, Proceedings, Part {VIII}},
    series    = {Lecture Notes in Computer Science},
    volume    = {12908},
    pages     = {76--86},
    publisher = {Springer},
    year      = {2021},
    url       = {https://doi.org/10.1007/978-3-030-87237-3\_8},
    doi       = {10.1007/978-3-030-87237-3\_8},
    timestamp = {Tue, 21 Dec 2021 17:19:23 +0100},
    biburl    = {https://dblp.org/rec/conf/miccai/GhamsarianTPSES21.bib},
    bibsource = {dblp computer science bibliography, https://dblp.org}
}
```

### 访问地址

[LensID](http://ftp.itec.aau.at/datasets/ovid/LensID/index.html)

### 关联数据集

- [cataract-21](/products/cataract-21)
- [cataract-101](/products/cataract-101)
- [IrisPupilSeg](/products/IrisPupilSeg)
- [CatInstSeg](/products/CatInstSeg)
- [CatRelComp](/products/CatRelComp)
- [CatRelDet](/products/CatRelDet)

### 相关论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| LensID: A CNN-RNN-Based Framework Towards Lens Irregularity Detection in Cataract Surgery Videos | MICCAI 2021 | Ghamsarian et al. |  [<i class="fa-solid fa-file"/>](https://link.springer.com/chapter/10.1007/978-3-030-87237-3_8)  | [<i class="fa-brands fa-github"/>](https://link.springer.com/chapter/10.1007/978-3-030-87237-3_8) |
