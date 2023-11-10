---
title: UBFC-Phys
subtitle: 由法国勃艮第-弗朗什孔泰大学收集的非接触式+接触式多模态数据集
description: 这里是描述
product_code: No.12132
layout: product
image: /assets/images/ubfc_phys.png
price: 开放获取
author: Sabour R M. et al.
date: 2023-01-01
rating: 4
features:
    -   label: 应激，非接触式，接触式，rPPG，PPG，EDA
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 徐俊聪维护
        icon: fa-id-card

---

UBFC-Phys 数据集是一个致力于心理生理学研究的公共多模态数据集。 56 名参与者遵循三步体验，通过休息任务 T1、言语任务 T2 和算术任务 T3 来承受社交压力。在体验过程中，参与者被拍摄并佩戴腕带，用于测量他们的血量脉搏 (BVP) 和皮肤电活动 (EDA) 信号。在体验开始之前和结束后，参与者填写了一份表格，以便计算他们自我报告的焦虑分数。该数据集包含参与者在三个任务期间测量的视频记录和 BVP / EDA 信号，以及在实验任务之前和之后计算的焦虑分数。

### 访问地址

[UBFC-Phys](https://ieee-dataport.org/open-access/ubfc-phys-2)

### 配置方式

```python
# Refering to https://github.com/Juncongood/MTASR for dataset preprocessing
import os
import torch
import numpy as np
import pandas as pd
import math
from scipy import signal
from torch.utils.data import Dataset
import yaml

yaml_file = "./setting.yaml"
cfg = yaml.safe_load(open(yaml_file, 'r'))
T1_s, T2_s, T3_s = cfg['dataset']['T1_selected'], cfg['dataset']['T2_selected'], cfg['dataset']['T3_selected']
T1_selected, T2_selected, T3_selected = set(T1_s), set(T2_s), set(T3_s)


def data_selected():
    stress_2 = T2_selected - (T2_selected & T3_selected)

    person_list, tasks, labels = [], [], []
    person_list += T1_s
    tasks += [1] * len(T1_s)
    person_list += T3_s
    tasks += [3] * len(T3_s)

    for s_2 in stress_2:
        person_list.append(s_2)
        tasks.append(2)

    labels += [0] * len(T1_s)
    labels += [1] * (len(T3_s) + len(stress_2))

    return person_list, tasks, labels


def data_selected_level(data_root):
    person_list, tasks, level = [], [], []
    # person_list += T1_s
    # tasks += [1] * len(T1_s)
    person_list += T2_s
    tasks += [2] * len(T2_s)
    person_list += T3_s
    tasks += [3] * len(T3_s)

    for person, task in zip(person_list, tasks):
        df_info = pd.read_csv(os.path.join(data_root, person, r'info_{0}.txt'.format(person)), header=None)
        if df_info.values[2][0] == "test":
            level += [1]
        else:
            level += [0]
    return person_list, tasks, level


class rPPG_Dataset(Dataset):
    def __init__(self, person_list, task_s, label_s):
        super(rPPG_Dataset, self).__init__()

        data = np.load(r"ubfc_phys.npy", allow_pickle=True)
        needed_data = None
        for _, (person, task, label) in enumerate(zip(person_list, task_s, label_s)):
            selected_data = data[(data[:, 0] == person) & (data[:, 1] == task) & (data[:, 3] == label)]
            if needed_data is None:
                needed_data = selected_data
            else:
                needed_data = np.concatenate((needed_data, selected_data))

        self.labels = torch.from_numpy(needed_data[:, 3].astype(np.uint8))
        self.tasks = torch.from_numpy(needed_data[:, 1].astype(np.uint8))
        self.level = torch.from_numpy(needed_data[:, 2].astype(np.uint8))
        self.PPG = torch.Tensor([ppg.astype(np.float32) for ppg in needed_data[:, 4]])
        self.peak = torch.Tensor([peak_.astype(np.uint8) for peak_ in needed_data[:, 5]])
        self.VPG = torch.Tensor([ppg.astype(np.float32) for ppg in needed_data[:, 6]])
        self.vpg_peak = torch.Tensor([peak_.astype(np.uint8) for peak_ in needed_data[:, 7]])
        self.HR = torch.Tensor(needed_data[:, 8].astype(np.float32))
        self.gaze = torch.Tensor(
            [[gaze_[i].astype(np.float32) for i in range(6)] for gaze_ in needed_data[:, 9:15]])
        self.pose = torch.Tensor(
            [[pose_[i].astype(np.float32) for i in range(3)] for pose_ in needed_data[:, 15:18]])

    def __len__(self):
        return len(self.labels)

    def __getitem__(self, item):
        return self.labels[item].to(torch.long), \
               self.tasks[item].to(torch.long), \
               self.level[item].to(torch.long), \
               self.PPG[item].unsqueeze(dim=0).to(torch.float), \
               self.peak[item].to(torch.float), \
               self.VPG[item].unsqueeze(dim=0).to(torch.float), \
               self.vpg_peak[item].to(torch.float), \
               self.HR[item].to(torch.float), \
               self.gaze[item].to(torch.float), \
               self.pose[item].to(torch.float)


class rPPG_Dataset_level(Dataset):
    def __init__(self, person_list, task_s, level_s):
        super(rPPG_Dataset_level, self).__init__()

        data = np.load(r"ubfc_phys.npy", allow_pickle=True)
        needed_data = None
        for _, (person, task, level_) in enumerate(zip(person_list, task_s, level_s)):
            selected_data = data[(data[:, 0] == person) & (data[:, 1] == task) & (data[:, 2] == level_)]
            if needed_data is None:
                needed_data = selected_data
            else:
                needed_data = np.concatenate((needed_data, selected_data))

        self.labels = torch.from_numpy(needed_data[:, 3].astype(np.uint8))
        self.tasks = torch.from_numpy(needed_data[:, 1].astype(np.uint8))
        self.level = torch.from_numpy(needed_data[:, 2].astype(np.uint8))
        self.PPG = torch.Tensor([ppg.astype(np.float32) for ppg in needed_data[:, 4]])
        self.peak = torch.Tensor([peak_.astype(np.uint8) for peak_ in needed_data[:, 5]])
        self.VPG = torch.Tensor([ppg.astype(np.float32) for ppg in needed_data[:, 6]])
        self.vpg_peak = torch.Tensor([peak_.astype(np.uint8) for peak_ in needed_data[:, 7]])
        self.HR = torch.Tensor(needed_data[:, 8].astype(np.float32))
        self.gaze = torch.Tensor(
            [[gaze_[i].astype(np.float32) for i in range(6)] for gaze_ in needed_data[:, 9:15]])
        self.pose = torch.Tensor(
            [[pose_[i].astype(np.float32) for i in range(3)] for pose_ in needed_data[:, 15:18]])

    def __len__(self):
        return len(self.labels)

    def __getitem__(self, item):
        return self.labels[item].to(torch.long), \
               self.tasks[item].to(torch.long), \
               self.level[item].to(torch.long), \
               self.PPG[item].unsqueeze(dim=0).to(torch.float), \
               self.peak[item].to(torch.float), \
               self.VPG[item].unsqueeze(dim=0).to(torch.float), \
               self.vpg_peak[item].to(torch.float), \
               self.HR[item].to(torch.float), \
               self.gaze[item].to(torch.float), \
               self.pose[item].to(torch.float)
```

### Benchmarks

| 任务                         | 数据集变体         | 最优模型        | 代码                                                     | 论文                                                                |
|----------------------------|---------------|-------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Stress State Recognition   | UBFC-Phys      | MTASR     | [<i class="fa-brands fa-github"/>](https://github.com/Juncongood/MTASR) | [<i class="fa-solid fa-file"/>]() |
| Stress level Recognition   | UBFC-Phys      | MTASR     | [<i class="fa-brands fa-github"/>](https://github.com/Juncongood/MTASR) | [<i class="fa-solid fa-file"/>]() |


### 小组论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Facial Video-based Non-contact Stress Recognition Utilizing Multi-Task Learning with Peak Attention | IEEE Journal of Biomedical and Health Informatics | Juncong Xu, Chen song, Zijie Yue, Shuai Ding | (Major Revision) |  |
