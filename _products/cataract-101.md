---
title: Cataract-101
subtitle: 由奥地利克拉根福大学ITEC研究团队收集的白内障手术视频数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/cataract-101.png
price: 开放获取
author: Schoeffmann et al.
date: 2018-06-01
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

白内障手术是眼科领域最常进行的显微手术之一，这种手术的目的是用人造晶状体取代人眼晶状体。整个手术在显微镜下进行，摄像头可以记录和存档整个过程。Cataract-101包含在奥地利Klinikum Klagenfurt录制的101个白内障手术视频，手术由四位外科医生实施，视频以25 fps拍摄，分辨率为720×540，平均时长为8.34分钟。视频所有帧都由资深眼科医生标注了10个手术阶段。

数据集仅用于**科研目的**，不能用于商业或其他用途。使用时还需引用以下论文:

```bibtex
@inproceedings{DBLP:conf/mmsys/SchoeffmannTSMP18,
  author    = {Klaus Schoeffmann and
               Mario Taschwer and
               Stephanie Sarny and
               Bernd M{\"{u}}nzer and
               Manfred J{\"{u}}rgen Primus and
               Doris Putzgruber},
  editor    = {Pablo C{\'{e}}sar and
               Michael Zink and
               Niall Murray},
  title     = {Cataract-101: video dataset of 101 cataract surgeries},
  booktitle = {Proceedings of the 9th {ACM} Multimedia Systems Conference, MMSys
               2018, Amsterdam, The Netherlands, June 12-15, 2018},
  pages     = {421--425},
  publisher = {{ACM}},
  year      = {2018},
  url       = {https://doi.org/10.1145/3204949.3208137},
  doi       = {10.1145/3204949.3208137},
  timestamp = {Wed, 21 Nov 2018 12:44:02 +0100},
  biburl    = {https://dblp.org/rec/conf/mmsys/SchoeffmannTSMP18.bib},
  bibsource = {dblp computer science bibliography, https://dblp.org}
}
```

### 访问地址

[Cataract-101](http://ftp.itec.aau.at/datasets/ovid/cat-101/)

### 配置方式

```python
# Refering to https://github.com/trigger26/SPSVR for dataset preprocessing
import os
import csv
import numpy as np
from operator import itemgetter

from torch.utils.data import Dataset, DataLoader, ConcatDataset
from torchvision.io import read_image

_FPS_CATARACTS = 25

class VideoClip(Dataset):
    def __init__(self, img_root, ann_path, num_class, video_name, n_frame, duration, fps, group_tfs=None, drop_phase_transition=False):
        self.img_root = img_root
        self.video_name = video_name
        self.n_frame = n_frame
        self.group_tfs = group_tfs

        frames = self.load_csv(ann_path, video_name)
        clips = self.clip_devide(frames, duration, fps)
        if drop_phase_transition:
            clips = self.filter_wo_phase_transition(clips)
        self.postprocess_label(clips, num_class)
        self.clips = clips


    def __getitem__(self, index):
        clip = self.clips[index]
        phase = clip["phase"]

        img_paths = clip["img"]        
        img_ids = clip["img_id"]

        # downsample
        inds = self.uniform_subsample(self.n_frame, len(img_paths))
        img_paths = img_paths[inds]
        img_ids = img_ids[inds]

        # read & transform
        imgs = self.group_tfs(
            [read_image(os.path.join(self.img_root, path)) for path in img_paths])

        return index, list(img_ids), imgs, phase


    def __len__(self):
        return len(self.clips)
    

    def load_csv(self, ann_path, video_name):
        res = []
        csv_path = os.path.join(ann_path, video_name + '.csv')
        with open(csv_path, 'r') as f:
            reader = csv.reader(f, delimiter=',')
            for row in reader:
                res.append({
                    "img": row[0],
                    "phase": int(row[1]),
                    "img_id": row[0].split('.')[0]
                })
        return res
    

    def uniform_subsample(self, sample_n, length):
        return np.linspace(0, length-1, sample_n, dtype=int)


    def filter_wo_phase_transition(self, clips):
        res = []
        for clip in clips:
            if clip["phase"].min() == clip["phase"].max():
                res.append(clip.copy())
        return res


    def postprocess_label(self, clips, num_classes):
        """ array[x, x, x] -> one hot """
        for clip in clips:
            label = int(clip["phase"][0])
            label = one_hot(label, num_classes)  # one hot
            clip["phase"] = label


    def clip_devide(self, frames, duration, fps):
        """ video to clips

            Args:
                frames: video frames metadata List
                duration: clip duration (seconds)
            
            Return: Clip metadata List
        """
        res = []
        start = 0
        while start <= len(frames) - duration * fps:
            end = start + duration * fps
            clip = frames[start: end]
            keys = clip[0].keys()  # ["img", "phase", "img_id"]
            clip_numpy = {}
            for k in keys:
                clip_numpy[k] = np.array(list(map(itemgetter(k), clip)))
            res.append(clip_numpy)
            start += duration * fps
        return res


def one_hot(a, num_classes):
    if isinstance(a, int):
        b = np.zeros(num_classes)
        b[a] = 1
        return b
    elif isinstance(a, np.ndarray):
        b = np.zeros((a.shape[0], num_classes))
        b[np.arange(a.shape[0]), a.astype(int)] = 1
        return b
    else:
        raise ValueError()

def _get_multi_video_dataset_cataract101(img_root, ann_path, num_class, video_names, n_frame, duration, group_tfs, drop_phase_transition):
    datasets = []
    for video_name in video_names:
        datasets.append(VideoClip(img_root, ann_path, num_class, video_name, n_frame, duration, _FPS_CATARACTS, group_tfs, drop_phase_transition))
    return ConcatDataset(datasets)

def get_dataloader(name, batch_size, shuffle, pin_memory, num_workers, **kwargs):
    if name == 'cataract101':
        dataset = _get_multi_video_dataset_cataract101(
            img_root=kwargs['data_dir'],
            ann_path=kwargs['label_dir'],
            num_class=kwargs['num_class'],
            video_names=kwargs['video_names'],
            n_frame=kwargs['n_frame'],
            duration=kwargs['duration'],
            group_tfs=kwargs['group_tfs'],
            drop_phase_transition=True
        )

    return DataLoader(dataset, batch_size, shuffle=shuffle, pin_memory=pin_memory, num_workers=num_workers)
```

### 关联数据集

- [cataract-21](/products/cataract-21)
- [IrisPupilSeg](/products/IrisPupilSeg)
- [CatInstSeg](/products/CatInstSeg)
- [CatRelComp](/products/CatRelComp)
- [CatRelDet](/products/CatRelDet)
- [LensID](/products/LensID)

### 相关论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| GLSFormer: Gated - Long, Short Sequence Transformer for Step Recognition in Surgical Videos | MICCAI 2023 | Nisarg A. Shah et al. |  [<i class="fa-solid fa-file"/>](https://link.springer.com/chapter/10.1007/978-3-031-43996-4_37)  | [<i class="fa-brands fa-github"/>](https://github.com/nisargshah1999/GLSFormer) |
| Dynamic CNNs Using Uncertainty To Overcome Domain Generalization for Surgical Instrument Localization | WACV 2022 | Markus Philipp et al. |  [<i class="fa-solid fa-file"/>](https://openaccess.thecvf.com/content/WACV2022/html/Philipp_Dynamic_CNNs_Using_Uncertainty_To_Overcome_Domain_Generalization_for_Surgical_WACV_2022_paper.html)  |  |


### Benchmarks

| 任务                         | 数据集变体         | 最优模型        | 代码                                                     | 论文                                                                |
|----------------------------|---------------|-------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Surgical phase recognition       | Cataract-101      | GLSFormer     | [<i class="fa-brands fa-github"/>](https://github.com/nisargshah1999/GLSFormer) | [<i class="fa-solid fa-file"/>](https://link.springer.com/chapter/10.1007/978-3-031-43996-4_37) |
| Domain Generalization for Surgical Instrument Localization | Cataract-101      | u-FUS |  | [<i class="fa-solid fa-file"/>](https://openaccess.thecvf.com/content/WACV2022/html/Philipp_Dynamic_CNNs_Using_Uncertainty_To_Overcome_Domain_Generalization_for_Surgical_WACV_2022_paper.html) |

### 小组论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Semantic-Preserving Surgical Video Retrieval with Phase and Behavior Coordinated Hashing | IEEE Transactions on Medical Imaging | Yuxuan Yang, Hao Wang, Jizhou Wang, Kai Dong, Shuai Ding |  [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/abstract/document/10269330/)  | [<i class="fa-brands fa-github"/>](https://github.com/trigger26/SPSVR) |
