---
title: CatInstSeg
subtitle: 由奥地利克拉根福大学ITEC研究团队收集的白内障手术视频实例分割数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/CatInstSeg.png
price: 开放获取
author: Fox et al.
date: 2020-07-28
rating: 4
features:
    -   label: 手术,眼科,视频,分割
        icon: fa-file-archive
    -   label: CC BY-NC 4.0协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 杨宇轩维护
        icon: fa-id-card

---

白内障手术是眼科领域最常进行的显微手术之一，这种手术的目的是用人造晶状体取代人眼晶状体。整个手术在显微镜下进行，摄像头可以记录和存档整个过程。CatInstSeg包含两个白内障手术视频的实例分割数据集。第一个数据集手工标注，第二个数据集基于公开的CaDIS数据集自动生成。第一个数据集包含11个类别，843张图像。第二个数据集包含15个类别，4738张图像。

数据集仅用于**科研目的**，不能用于商业或其他用途。使用时还需引用以下论文:

```bibtex
@inproceedings{DBLP:conf/cbms/FoxTS20,
  author    = {Markus Fox and
               Mario Taschwer and
               Klaus Schoeffmann},
  editor    = {Alba Garc{\'{\i}}a Seco de Herrera and
               Alejandro Rodr{\'{\i}}guez Gonz{\'{a}}lez and
               K. C. Santosh and
               Zelalem Temesgen and
               Bridget Kane and
               Paolo Soda},
  title     = {Pixel-Based Tool Segmentation in Cataract Surgery Videos with Mask
               {R-CNN}},
  booktitle = {33rd {IEEE} International Symposium on Computer-Based Medical Systems,
                {CBMS} 2020, Rochester, MN, USA, July 28-30, 2020},
  pages     = {565--568},
  publisher = {{IEEE}},
  year      = {2020},
  url       = {https://doi.org/10.1109/CBMS49503.2020.00112},
  doi       = {10.1109/CBMS49503.2020.00112},
  timestamp = {Thu, 10 Sep 2020 16:38:14 +0200},
  biburl    = {https://dblp.org/rec/conf/cbms/FoxTS20.bib},
  bibsource = {dblp computer science bibliography, https://dblp.org}
}
```

### 数据集1

从Cataract-101数据集中精心挑选视频帧，并进行器械分割的手工标注，标注格式为COCO。数据集共包含843张图像，标注了11个器械类别的分割区域。

| Class ID | Instrument              |
|----------|-------------------------|
| i1       | slit knife              |
| i2       | angled incision knife   |
| i3       | katena forceps          |
| i7       | 27 gauge cannula        |
| i8       | capsulorhexis forceps   |
| i9       | cannula                 |
| i10      | phaco tip               |
| i11      | spatula                 |
| i12      | I/A handpiece           |
| i16      | cartridge               |
| i20      | eye retractors          |

示例：

![](/assets/images/CatInstSeg-example1.png)

### 数据集2

基于公开的CaDIS数据集自动生成实例分割标注，共有4738张图像，标注了15个器械类别的分割区域。

器械类别：

- A/I handpiece   
- Bonn forceps    
- Cap. cystotome  
- Cap. forceps    
- Charleux cannula
- Hydro. cannula  
- Lens injector   
- Micromanipulator
- Phaco. handpiece
- Primary knife   
- Rycroft cannula 
- Secondary knife 
- Suture needle   
- Visco. cannula  
- Viter. handpiece

示例：

![](/assets/images/CatInstSeg-example2.png)

由于数据集2使用了公开的CaDIS数据集，因此使用时还需引用以下论文:

E. Flouty, A. Kadkhodamohammadi, I. Luengo, F. Fuentes-Hurtado, H. Taleb, S. Barbarisi, G. Quellec, and D. Stoyanov. Cadis: Cataract dataset for image segmentation.

### 访问地址

[CatInstSeg](http://ftp.itec.aau.at/datasets/ovid/InSegCat/index.html)

### 关联数据集

- [cataract-21](/products/cataract-21)
- [cataract-101](/products/cataract-101)
- [IrisPupilSeg](/products/IrisPupilSeg)
- [CatRelComp](/products/CatRelComp)
- [CatRelDet](/products/CatRelDet)
- [LensID](/products/LensID)

### 相关论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Pixel-Based Tool Segmentation in Cataract Surgery Videos with Mask R-CNN | 2020 IEEE 33rd International Symposium on Computer-Based Medical Systems (CBMS) | Fox et al. |  [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/abstract/document/9183116)  |  |
| Localized Questions in Medical Visual Question Answering | MICCAI 2023 | Tascon-Morales et al. |  [<i class="fa-solid fa-file"/>](https://link.springer.com/chapter/10.1007/978-3-031-43895-0_34)  | [<i class="fa-brands fa-github"/>](https://github.com/sergiotasconmorales/locvqa) |
