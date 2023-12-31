---
title: 海事船员多模态数据集
subtitle: 由合肥工业大学联合四川大学华西医院团队收集的海事船员多模态数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/hs.png
price: 自建自有
author: 2020年交通运输部海事局科技项目“中国船员健康感知与干预研究” (2020.6-2021.6, 0745-2041CCIEC016)
date: 2023-01-01
rating: 4
features:
    -   label: 船员，睡眠质量，焦虑，疲劳
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 莫海淼、刘子豪维护
        icon: fa-id-card

---

船员在执行长远航任务过程中受随浪颠簸、 密闭环境、 震动噪声、 昼夜节律改
变、 饮食单调、 远离社会等严峻健康威胁， 易触发营养、 疾病、 疲劳、 抑郁、 认知
等多维度的身心健康风险。其中包含量表信息如下：

| 量表                       | 上船前 | 上船中 | 上船后 |
| ---------------------------- | ------ | ------ | ------ |
| PSQI 睡眠质量评定量表 | √    | √    | √    |
| HPLP-Ⅱ健康促进生活方式量表 | √    |        | √    |
| PHQ2/PHQ9 抑郁情绪评定量表 | √    | √    | √    |
| DASS-21 情绪状态综合评定量表 | √    | √    | √    |
| GAD-7 焦虑情绪评定量表 | √    | √    | √    |
| FAD-GF 家庭功能评定量表 | √    |        | √    |
| SSRS 社会支持评定量表 | √    |        | √    |
| 工作环境                 |        |        | √    |
| 娱乐方式                 | √    |        | √    |
| SBQ-R 生命态度评定量表 | √    |        | √    |
| MFI 多维疲劳量表       |   √      |        |   √      |

### 访问地址

PS服务器：`/home/ps/outmounts/data2/MoHaimiao/Dataset_HaiShi_project/`

### 配置方式


1. 视频中提取语音：
    1. 安装依赖项：pip install moviepy
    2. python实现代码：
        ```python
        from moviepy.editor import *
        video = VideoFileClip('test1.mp4')
        audio = video.audio
        audio.write_audiofile('test1.mp3')
        ```
2. 音频中分离人声：

   1. 文件目录：
        ```
        \sound_separation\output
        \sound_separation\pretrained_models\2stems   (2stems预训练模型)
        \sound_separation\pretrained_models\4stems   (4stems预训练模型)
        ```
   2. 安装依赖项参考https://github.com/deezer/spleeter，具体操作如下：
        ```
        # install dependencies using conda
        conda install -c conda-forge ffmpeg libsndfile
        # install spleeter with pip
        pip install spleeter
        ```
   3. 安装好依赖项之后，在命令行模式下，切换到/sound_separation目录，执行以下指令：
        ```
        # separate the example audio into two components
        spleeter separate -p spleeter:4stems -o output -i test.mp3 # command指令
        # test.mp3文件需要根据实际情况修改文件名，该文件在主目录sound_separation
        # -o output：输出路径output为当前路径下的子目录；也可以为绝对路径
        # -i test.mp3：也可以是绝对路径下的音频文件
        ```
        或者执行以下python代码：
        ```python
        import os
        command = 'spleeter separate -p spleeter:4stems -o output -i test.mp3'
        os.system(command)
        ```


    4. 如果出现加载模型有误，很有可能是没有下载spleeter的2stems/4stems预训练模型，
    因此需要从网上搜索"4stems.tar.gz"或者“2stems.tar.gz”并下载预训练模型，
    解压之后，放在对应的/pretrained_models/2stems或者/pretrained_models/4stems目录下，即可解决问题。
    该问题的debug参考deezer/spleeter#306
       1. 2stems预训练模型下载地址:https://github.com/deezer/spleeter/releases/download/v1.4.0/2stems.tar.gz
       2. 4stems预训练模型下载地址:https://github.com/deezer/spleeter/releases/download/v1.4.0/4stems.tar.gz


3. 行为特征提取：openface 2.0工具包，包含：AUs+头部位姿+眼睛注视特征
4. iPPG提取: 参考physformer的代码 https://github.com/ZitongYu/PhysFormer
5. 文本特征提取：bert预训练模型 https://huggingface.co/bert-base-uncased/tree/main
 



### Benchmarks

自建数据集：无


### 小组论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
|SFF-DA: Sptialtemporal Feature Fusion for Detecting Anxiety Nonintrusively | IEEE Transactions on Instrumentation and Measurement | Haimiao Mo, Yuchen Li, Peng Han, Xiao Liao, Wei Zhang,  Shuai Ding |  [<i class="fa-solid fa-file"/>](https://arxiv.org/abs/2208.06411)  |  |
| A Multimodal Data-driven Framework for Anxiety Screening | IEEE Transactions on Instrumentation and Measurement | Haimiao Mo, Siu Cheung Hui, Xiao Liao, Li Yuchen, Wei Zhang, Shuai Ding |  [<i class="fa-solid fa-file"/>](https://arxiv.org/abs/2303.09041)  |  |
