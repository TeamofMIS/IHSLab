---
title: VIPL-HR
subtitle: 中国科学院计算技术研究所收集的多场景面部视频数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/VIPL-HR.png
price: 可申请获取
author: Xuesong Niu et al.
date: 2023-01-01
rating: 4
features:
    -   label: 非接触式，视频，生理
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 张文杰维护
        icon: fa-id-card

---

VIPL-HR 数据库是一个在约束较少的情况下根据人脸视频进行远程心率 (HR) 估计的数据库。它包含 107 个主题的 2,378 个可见光视频 (VIS) 和 752 个近红外 (NIR) 视频。考虑了九种不同的条件，包括各种头部运动和照明条件。所有视频均使用 Logitech C310、RealSense F200 和华为 P9 智能手机的前置摄像头录制，金标准 HR 使用 CONTEC CMS60C BVP 传感器（FDA 批准的设备）录制。

### 访问地址

[VIPL-HR](http://vipl.ict.ac.cn/en/resources/databases/201901/t20190104_34800.html)

### 配置方式

```python
import torch
import os
import numpy as np
import pandas as pd
from torch.utils.data import Dataset, DataLoader
from torchvision import transforms
import cv2
import random

rootpath = "/home/som/8T/lulu/10T/qyt/Data/VIPL/original_face/viplface128/"    # 训练集根目录
clip_frames = 160                #单个视频切割长度
# 地址 开始帧 帧率 心率值 5 ppg
class Normaliztion(object):
    """
        same as mxnet, normalize into [-1, 1]
        image = (image - 127.5)/128
    """

    def __call__(self, sample):
        video_x, clip_average_HR, ecg_label, frame_rate = sample['video_x'], sample['clip_average_HR'], sample['ecg'], \
                                                          sample['frame_rate']
        new_video_x = (video_x - 127.5) / 128      #对每一个像素点进行归一化操作
        return {'video_x': new_video_x, 'clip_average_HR': clip_average_HR, 'ecg': ecg_label, 'frame_rate': frame_rate}


class RandomHorizontalFlip(object):                   #以0.5的概率水平翻转图像
    """Horizontally flip the given Image randomly with a probability of 0.5."""

    def __call__(self, sample):
        video_x, clip_average_hr, ecg_label, frame_rate = sample['video_x'], sample['clip_average_HR'], sample['ecg'], \
                                                          sample['frame_rate']

        h, w = video_x.shape[1], video_x.shape[2]               #实现图像的翻转
        new_video_x = np.zeros((clip_frames, h, w, 3))

        p = random.random()
        if p < 0.5:
            #  print('Flip')
            for i in range(clip_frames):
                # video
                image = video_x[i, :, :, :]
                image = cv2.flip(image, 1)            #翻转函数
                new_video_x[i, :, :, :] = image

            return {'video_x': new_video_x, 'clip_average_HR': clip_average_hr, 'ecg': ecg_label,
                    'frame_rate': frame_rate}
        else:
            #  print('no Flip')
            return {'video_x': video_x, 'clip_average_HR': clip_average_hr, 'ecg': ecg_label, 'frame_rate': frame_rate}


class ToTensor(object):                  #实例化即调用
    """
        Convert ndarrays in sample to Tensors.
        process only one batch every time
    """

    def __call__(self, sample):
        video_x, clip_average_hr, ecg_label, frame_rate = sample['video_x'], sample['clip_average_HR'], sample['ecg'], \
                                                          sample['frame_rate']

        # swap color axis because
        # numpy image: (batch_size) x depth x H x W x C
        # torch image: (batch_size) x C x depth X H X W
        video_x = video_x.transpose((3, 0, 1, 2))  # ((0, 4, 1, 2, 3))
        video_x = np.array(video_x)

        clip_average_hr = np.array(clip_average_hr)

        frame_rate = np.array(frame_rate)

        return {'video_x': torch.from_numpy(video_x.astype(np.float)).float(),
                'clip_average_HR': torch.from_numpy(clip_average_hr.astype(np.float)).float(),
                'ecg': torch.from_numpy(np.array(ecg_label).astype(np.float)).float(),
                'frame_rate': torch.from_numpy(frame_rate.astype(np.float)).float()}

# 定义MMVS训练数据集
class Vipl(Dataset):
    def __init__(self,scale,info_list,transform=None):     #transform参数用于传递数据增强的操作，例如将数据转换为张量和标准化等操作
        self.info_df = pd.read_csv(info_list,delimiter=' ',header=None)
        # self.rootpath = rootpath
        self.transform = transform
        self.scale = scale

    def __len__(self):
        return(len(self.info_df))

    def __getitem__(self, index):
        videopath = str(self.info_df.iloc[index][0])

        start_frame = self.info_df.iloc[index,1]
        frame_rate = self.info_df.iloc[index,2]
        clip_average_HR = self.info_df.iloc[index,3]
        ecg = self.info_df.iloc[index,5:5+clip_frames]
        video_x = self.get_single_video(videopath = videopath,start_frame = start_frame)
        sample = {'video_x': video_x, 'frame_rate': frame_rate, 'ecg': ecg,
                  'clip_average_HR': clip_average_HR}

        if self.transform:
            sample = self.transform(sample)
        return sample

    def get_single_video(self,videopath,start_frame):
        pngpath = videopath
        video = np.zeros((clip_frames, self.scale, self.scale, 3))     # numpy image: (batch_size) x depth x H x W x C;torch image: (batch_size) x C x depth X H X W

        imageid = start_frame

        for i in range(clip_frames):
            image_name = 'face_'+ str(imageid+i)+'.png'
            image_path = os.path.join(pngpath,image_name)
            tmp_image = cv2.imread(image_path)

            if tmp_image is None:
                tmp_image = video[i-1,:,:,:]

            tmp_image = cv2.resize(tmp_image, (self.scale, self.scale),interpolation=cv2.INTER_CUBIC)       #采用双立方插值法进行图像缩放，变为128*128维度
            video[i,:,:,:] = tmp_image
        return video

```

