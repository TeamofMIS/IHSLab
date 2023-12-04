---
title: SEER(Surveillance, Epidemiology, and End Results)
subtitle: 由美国国家癌症研究所（National Cancer Institute，NCI）创建的一个公共数据库和研究资源
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/SEER Stat.png
price: 可申请获取
author: NCI
date: 1973-01-01
rating: 4
features:
    -   label: 癌症、人口学特征、癌症类型、生存状况
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 崔海涛维护
        icon: fa-id-card
---

SEER由美国国立癌症研究所(National Cancer Institute，NCI)于1973年所建立，是北美最具代表性的大型肿瘤登记注册数据库。数据库所涉及的肿瘤划分为9类:乳腺、结肠&直肠、其他消化系统、女性生殖、淋巴&白血病、男性生殖、呼吸系统、泌尿系统及其它尚未确指的类型。数据记录中包括患者的注册编号、个人信息、原发病灶部位、肿瘤尺寸、肿瘤编码、治疗方案、死亡原因等信息。SEER提交的数据将在每年的十一月进行检查，在第二年的四月中旬开放注册。

### 访问地址

[SEER](https://seer.cancer.gov/)

### 使用指南

SEER数据库有专门的软件对数据进行操作，即SEERStat。通过SEETStat筛选癌症相关数据的步骤如下所示：
1、在官网下载SEERStat之后，使用注册的用户名和密码进行登录。
2、登录后，点击表格按钮（case listing session），这里面有每个肿瘤患者的个人信息（如性别、年龄、TNM 分期等），常规发表 SEER 数据库相关的文章，都是用这部分的数据。
3、在点击上述所说的表格按钮后会出现四个选项：“Data”、“Selection”、“Table”、“Output”。“Data”界面中可以选择任意想要查阅的数据集。数据集的名字上会介绍参与此数据集的机构数量及起始时间。可根据需要进行选择。
4、选好数据集后点击“Selection”，进入下一个界面。selection其实就是选择病例筛选的条件。再点击Edit按钮，会有很多选项，选择你需要的病例范围、肿瘤类型等等。注：在官网下载“CS coding instructions”的文件，里面详细罗列了该数据库使用的每个coding的解释。
5、第三栏table选择需要的临床信息。右边sort那一栏选择加入patient ID，这样导出的所有病例都是按ID号排序的，就不会混乱。左侧column那栏可以选择需要的变量，SEER的信息登记的非常全面，常用的年龄、病理类型、AJCC分期、生存时间、死因、治疗等等数据都有。
6、最后点击工具栏的闪电按钮（execute）进行病例数的下载。第四栏Output，指的是输出文件的文件名，可以随便命名。点击闪电按钮，即可输出想要的文件。字典功能可以提供部分变量的含义，会提供相关的链接。

### 相关论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Application of a novel machine learning framework for predicting non-metastatic prostate cancer-specific mortality in men using the Surveillance, Epidemiology, and End Results (SEER) database | The Lancet Digital Health | Changhee Lee et al. |  [<i class="fa-solid fa-file"/>](https://www.thelancet.com/journals/landig/article/PIIS2589-7500(20)30314-9/fulltext)  | 
| Incidence, risk factors, and mortality of atrial fibrillation in breast cancer: a SEER-Medicare analysis | European Heart Journal | Avirup Guha et al. |  [<i class="fa-solid fa-file"/>](https://academic.oup.com/eurheartj/article/43/4/300/6427396#google_vignette)  |  
