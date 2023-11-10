---
title: CholecT50
subtitle: 由法国斯特拉斯堡大学CAMMA研究团队收集的细粒度手术活动识别数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/cholect50.png
price: 可申请获取
author: Nwoye et al.
date: 2023-01-01
rating: 4
features:
    -   label: 手术，视频，器械，动作，组织，三元组
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 杨宇轩维护
        icon: fa-id-card

---

CholecT50是腹腔镜胆囊切除术的内窥镜视频数据集，旨在支持腹腔镜手术中细粒度活动识别的研究。这些视频于法国斯特拉斯堡收集。数据集以1 fps从视频中提取图像，并以 <instrument, verb, target> 格式标注有关手术活动的三元组信息，还提供了阶段标签。为5个视频提供了器械尖端bounding box形式的空间注释，而且为每个bounding box提供了三元组匹配标签。该数据集包含50个视频，其中包括来自Cholec80数据集的45个视频和来自同一手术的Cholec120内部数据集的5个视频。

### 访问地址

[CholecT50](http://camma.u-strasbg.fr/datasets)

### 配置方式

见官方 GitHub 仓库 [<i class="fa-brands fa-github"/> CAMMA-public/cholect50](https://github.com/CAMMA-public/cholect50)

### Benchmarks

| 任务                         | 数据集变体         | 最优模型        | 代码                                                     | 论文                                                                |
|----------------------------|---------------|-------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Action Triplet Recognition       | CholecT50      | 	Rendezvous     | [<i class="fa-brands fa-github"/>](https://github.com/camma-public/rendezvous) | [<i class="fa-solid fa-file"/>](https://www.sciencedirect.com/science/article/pii/S1361841522000846) |
| Action Triplet Detection | CholecT50      | ResNet-CAM-YOLOv5 |  | [<i class="fa-solid fa-file"/>](https://www.sciencedirect.com/science/article/pii/S1361841523001482) |


### 小组论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| CholecTriplet2021: A benchmark challenge for surgical action triplet recognition | Medical Image Analysis | Chinedu Innocent Nwoye et al. |  [<i class="fa-solid fa-file"/>](https://www.sciencedirect.com/science/article/pii/S1361841523000646)  |  |
