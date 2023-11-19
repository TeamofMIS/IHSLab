---
title: CatRelComp
subtitle: 由奥地利克拉根福大学ITEC研究团队收集用于白内障手术视频压缩的标注数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/CatSegComp.jpg
price: 开放获取
author: Ghamsarian et al.
date: 2020-10-01
rating: 4
features:
    -   label: 手术，眼科，视频，压缩
        icon: fa-file-archive
    -   label: CC BY-NC 4.0协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 杨宇轩维护
        icon: fa-id-card

---

CatRelComp是一个用于白内障手术视频压缩的注释数据集，包含2个子集。

### 空闲帧识别子集

- 来源：Cataract-101数据集中的22个视频
- 包含18个用于训练,4个用于测试
- 每个视频样本了500个空闲帧和500个动作帧
- 训练集18000张图像，测试集4000张图像

空闲帧示例：

![](/assets/images/CatSegComp-example1.jpg)

动作帧示例：

![](/assets/images/CatSegComp-example2.jpg)

### 眼角膜和器械分割子集

- 来源：Cataract-101数据集中的11个视频  
- 使用Supervisely标注了262张眼角膜图像和216张器械图像的分割区域

示例：

![](/assets/images/CatSegComp-example3.jpg)

数据集仅用于**科研目的**,不能用于商业用途。使用时需引用以下论文:

```bibtex
@inproceedings{DBLP:conf/mm/GhamsarianATTS20,
  author    = {Negin Ghamsarian and
               Hadi Amirpour and
               Christian Timmerer and
               Mario Taschwer and
               Klaus Sch{\"{o}}ffmann},
  editor    = {Chang Wen Chen and
               Rita Cucchiara and
               Xian{-}Sheng Hua and
               Guo{-}Jun Qi and
               Elisa Ricci and
               Zhengyou Zhang and
               Roger Zimmermann},
  title     = {Relevance-Based Compression of Cataract Surgery Videos Using Convolutional
                 Neural Networks},
  booktitle = {\{MM\} '20: The 28th {ACM} International Conference on Multimedia, Virtual
               Event / Seattle, WA, USA, October 12-16, 2020},
  pages     = {3577--3585},
  publisher = {{ACM}},
  year      = {2020},
  url       = {https://doi.org/10.1145/3394171.3413658},
  doi       = {10.1145/3394171.3413658},
  timestamp = {Wed, 26 Jan 2022 09:31:46 +0100},
  biburl    = {https://dblp.org/rec/conf/mm/GhamsarianATTS20.bib},
  bibsource = {dblp computer science bibliography, https://dblp.org}
}
```

### 访问地址  

[CatRelComp](http://ftp.itec.aau.at/datasets/ovid/CatRelevanceCompression/index.html)

### 关联数据集

- [cataract-21](/products/cataract-21)
- [cataract-101](/products/cataract-101)
- [IrisPupilSeg](/products/IrisPupilSeg)
- [CatInstSeg](/products/CatInstSeg)
- [CatRelDet](/products/CatRelDet)
- [LensID](/products/LensID)

### 相关论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Relevance-Based Compression of Cataract Surgery Videos Using Convolutional Neural Networks | ACM MM '20 | Ghamsarian et al. |  [<i class="fa-solid fa-file"/>](https://dl.acm.org/doi/10.1145/3394171.3413658)  |  |
