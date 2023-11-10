---
title: UBFC-rPPG
subtitle: 由法国勃艮第-弗朗什孔泰大学所收集的多场景面部视频数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/UBFC-rPPG.png
price: 可申请获取
author: S. Bobbia et al.
date: 2019-06-01
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

公共数据库UBFC-RPPG提供了使用网络摄像头（Logitech C920 HD pro）收集的50个视频数据以及由透射式脉搏血氧计CMS50E测量的PPG（脉搏率和SpO2）信息。 参与者与摄像机之间的距离约为1 m，视频以30 fps 的帧速率和640 × 480 的分辨率录制。 在不同的地方进行数据收集，并使用环境光作为照明源。 参与者的肤色各不相同，其中有留着胡须的男人，也有戴着眼镜的人。 该数据库由两个数据集组成。 第一个数据集中包含八个视频。 记录过程中，参与者保持静止状态，其中一名参与者在测量实验前进行练习，而另一名参与者则不进行练习。 第二个数据集包含 42 个视频。 在这个数据集中，参与者在测量过程中需要玩数学游戏，以模拟人与计算机之间交互等现实情况。

### 访问地址

[UBFC-rPPG](https://sites.google.com/view/ybenezeth/ubfcrppg)

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

class UBFC(Dataset):
    def __init__(self,scale,info_list,transform = None):
        self.info_df = pd.read_csv(info_list,delimiter=' ',header=None)
        # self.rootpath = rootpath
        self.transform = transform
        self.scale = scale

    def __len__(self):
        return(len(self.info_df))

    def __getitem__(self, index):
        # videopath = os.path.join(self.rootpath,str(self.info_df.iloc[index][0]))       #视频路径    rootpath + 视频文件
        # videopath = os.path.join(self.rootpath,str(self.info_df.iloc[index][0]))
        videopath = str(self.info_df.iloc[index][0])
        start_frame = self.info_df.iloc[index,1]
        frame_rate = self.info_df.iloc[index,2]
        clip_average_HR = self.info_df.iloc[index,3]
        # ecg = self.info_df.iloc[index,5:5+clip_frames]
        ecg = self.info_df.iloc[index, 5: ]
        video_x = self.get_single_video(videopath = videopath,start_frame = start_frame)
        sample = {'video_x': video_x, 'frame_rate': frame_rate, 'ecg': ecg,
                  'clip_average_HR': clip_average_HR}

        if self.transform:
            sample = self.transform(sample)
        return sample

    def get_single_video(self,videopath,start_frame):        #从startframe开始，将160帧存到video中，video是一个numpy数组
        pngpath = videopath
        # video = np.zeros(clip_frames, self.scale, self.scale, 3)     # numpy image: (batch_size) x depth x H x W x C;torch image: (batch_size) x C x depth X H X W
        video = np.zeros((clip_frames, self.scale, self.scale, 3))
        # print(video.shape)
        startframe = start_frame
        video_capture = cv2.VideoCapture(pngpath)
        count = 0
        while True:
            # 读取一帧视频
            ret, frame = video_capture.read()
            frame = cv2.resize(frame, (self.scale, self.scale), interpolation=cv2.INTER_CUBIC)
            # if frame == None:
            #     frame = video[count-1, :, :, :]
            if count == startframe:
                video[count, :, :, :] = frame
            count = count + 1
            if count == clip_frames:
                break
        return video


        # 释放视频捕获对象和关闭显示窗口
        video_capture.release()
        for i in range(clip_frames):
            image_name = 'face_'+ str(imageid+i)+'.jpg'
            image_path = os.path.join(pngpath,image_name)
            tmp_image = cv2.imread(image_path)

            if tmp_image == None:
                tmp_image = cv2.imread(self.rootpath + '20201016/1/face_1.png')

            tmp_image = cv2.resize(tmp_image, (self.scale, self.scale),interpolation=cv2.INTER_CUBIC)       #采用双立方插值法进行图像缩放，变为128*128维度
            video[i,:,:,:] = tmp_image
        return video

```
