---
title: 非接触式多模态多生命体征数据集
subtitle: 在实验室和安医二附院内分泌科室环境下采集的非接触式多模态多生命体征数据集
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/MMVS.png
price: 自建自有
author: 
date: 2021-01-10
rating: 4
features:
    -   label: 非接触式，多模态，心率，呼吸率，血压
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 秦玉婷维护
        icon: fa-id-card

---

包含对129位受试者采集的762组数据，每组数据包含时长为一分钟的可见光面部视频、热红外面部视频、对应时间段内的PPG信号和心率、呼吸波信号、收缩压、舒张压标签，以及各位受试者的性别、年龄、身高、体重信息。可见光面部视频由Intel Realsense D435i深度相机采集（1920×1080@25fps），热红外面部视频由高德热红外相机采集（640×480@25fps），PPG信号采样频率为60Hz，呼吸波信号采样频率为50Hz。

### 访问地址

SOM服务器：`/home/som/8T/lab-data_new/Storage/`

### 配置方式

1.PPG标签处理

```python
def generate_mmvs_trainset_expm0():
    dataset_path = '/home/som/8T/lab-data_new/Storage/'
    image_root_path = '/home/som/8T/lulu/10T/qyt/Data/mmvsface/'
    traiset_record_file = open('/home/som/2T/qyt/rppg_transformer/mmvs_expm/mmvs_expm0_facetorppg_train_whole.txt', 'w')

    for date in os.listdir(image_root_path):
        for person in os.listdir(image_root_path + date):
            id = int(person.split('_')[0])
            # print(id)
            if id not in [64, 127, 74, 25, 33, 22, 55, 7, 68, 52, 97, 56, 99, 109, 106, 110, 86, 4, 11, 88, 10, 44, 84, 70, 17, 87, 122, 57, 34, 47, 71, 53, 21, 9, 72, 113, 66, 15, 18, 28, 59, 93, 23, 126, 92, 2, 79, 108, 48, 26, 104, 13, 80, 78, 101, 125, 51, 58, 115, 40, 1, 43, 73, 95, 69, 85, 63, 100, 16, 35, 42, 89, 75, 83, 6, 12, 19, 20, 29, 32, 37, 39, 45, 62, 77, 91, 103, 105, 116, 121, 123]:
                continue

            frame_path = date + '/' + person
            image_path = image_root_path + frame_path
            image = os.listdir(image_path)
            time = len(image)/25

            ppg_path = dataset_path + date + '/physiology/' + person + '/pulsewave.xml'
            ppg = extract_pulsewave(ppg_path)
            ppg_fs = len(ppg)/time  # 实际帧率可能小于60
            ppg_down = signal_down_sample(ppg, ppg_fs, 25)
            # ppg_down = zero_mean(ppg_down)
            ppg_down = detrend(np.array(ppg_down))
            ppg_down = (ppg_down - np.min(ppg_down)) / (np.max(ppg_down) - np.min(ppg_down))

            hr_path = dataset_path + date + '/physiology/' + person + '/pulse.xml'
            valid, hr = readpulsexml_whole(hr_path)

            #  or np.mean(np.array(hr)) < 45 or np.mean(np.array(hr)) > 150
            if valid == False:
                continue

            clip_number = (len(image)-60)//160

            flag = True
            for clip in range(clip_number):
                image_id = 61 + clip * 160
                if int((image_id + 159) / 25) > len(hr):
                    print('1', frame_path, clip + 1, int((image_id + 159) / 25), len(hr))
                    flag = False
                    continue

            if not flag:
                continue

            for clip in range(clip_number):
                image_id = 61 + clip * 160

                hr_record = int(np.mean(hr[int(image_id/25):int((image_id+159)/25)+1]))
                if hr_record < 40 or hr_record >= 180:
                    continue

                ppg_final = ppg_down[image_id:image_id + 160].tolist()
                while len(ppg_final) < 160:
                    ppg_final.append(2*ppg_final[-1]-ppg_final[-2])
                hr_label = TorchLossComputer.cross_entropy_power_spectrum_forward_pred(torch.tensor(ppg_final), 25) + 40
                if abs(hr_label - float(hr_record)) > 5:
                    hr_record = hr_label
                    # continue

                traiset_record_file.write(frame_path)
                traiset_record_file.write(' ')
                traiset_record_file.write(str(image_id))
                traiset_record_file.write(' 25 ')
                traiset_record_file.write('{:.6f}'.format(hr_record))
                traiset_record_file.write(' 5')

                for sample in ppg_final:
                    traiset_record_file.write(' %.6f' % sample)
                traiset_record_file.write('\n')
                traiset_record_file.flush()
    traiset_record_file.flush()
    traiset_record_file.close()
```

2.数据加载

```python
clip_frames = 160

class Normaliztion(object):
    """
        same as mxnet, normalize into [-1, 1]
        image = (image - 127.5)/128
    """

    def __call__(self, sample):
        video_x, clip_average_HR, ecg_label, frame_rate = sample['video_x'], sample['clip_average_HR'], sample['ecg'], 
                                                          sample['frame_rate']
        new_video_x = (video_x - 127.5) / 128
        return {'video_x': new_video_x, 'clip_average_HR': clip_average_HR, 'ecg': ecg_label, 'frame_rate': frame_rate}


class RandomHorizontalFlip(object):
    """Horizontally flip the given Image randomly with a probability of 0.5."""

    def __call__(self, sample):
        video_x, clip_average_hr, ecg_label, frame_rate = sample['video_x'], sample['clip_average_HR'], sample['ecg'], 
                                                          sample['frame_rate']

        h, w = video_x.shape[1], video_x.shape[2]
        new_video_x = np.zeros((clip_frames, h, w, 3))

        p = random.random()
        if p < 0.5:
            #  print('Flip')
            for i in range(clip_frames):
                # video
                image = video_x[i, :, :, :]
                image = cv2.flip(image, 1)
                new_video_x[i, :, :, :] = image

            return {'video_x': new_video_x, 'clip_average_HR': clip_average_hr, 'ecg': ecg_label,
                    'frame_rate': frame_rate}
        else:
            #  print('no Flip')
            return {'video_x': video_x, 'clip_average_HR': clip_average_hr, 'ecg': ecg_label, 'frame_rate': frame_rate}


class ToTensor(object):
    """
        Convert ndarrays in sample to Tensors.
        process only one batch every time
    """

    def __call__(self, sample):
        video_x, clip_average_hr, ecg_label, frame_rate = sample['video_x'], sample['clip_average_HR'], sample['ecg'], 
                                                          sample['frame_rate']

        # swap color axis because
        # numpy image: (batch_size) x depth x H x W x C
        # torch image: (batch_size) x C x depth x H x W
        video_x = video_x.transpose((3, 0, 1, 2))  # ((0, 4, 1, 2, 3))
        video_x = np.array(video_x)

        clip_average_hr = np.array(clip_average_hr)

        frame_rate = np.array(frame_rate)

        return {'video_x': torch.from_numpy(video_x.astype(np.float)).float(),
                'clip_average_HR': torch.from_numpy(clip_average_hr.astype(np.float)).float(),
                'ecg': torch.from_numpy(ecg_label.astype(np.float)).float(),
                'frame_rate': torch.from_numpy(frame_rate.astype(np.float)).float()}


# train.txt: 20201021/1_1 61 25 93.750000 5 ppg
class MMVSTrain(Dataset):

    def __init__(self, info_list, root_dir, transform=None):

        self.landmarks_frame = pd.read_csv(info_list, delimiter=' ', header=None)
        self.root_dir = root_dir
        self.transform = transform

    def __len__(self):
        return len(self.landmarks_frame)

    def __getitem__(self, idx):
        # print(self.landmarks_frame.iloc[idx, 0])
        video_path = os.path.join(self.root_dir, str(self.landmarks_frame.iloc[idx, 0]))

        start_frame = self.landmarks_frame.iloc[idx, 1]

        frame_rate = self.landmarks_frame.iloc[idx, 2]

        clip_average_hr = self.landmarks_frame.iloc[idx, 3]

        p = random.random()
        if p < 0.5 and start_frame == 0:  # sampling aug     p < 0.5
            ecg_label = self.landmarks_frame.iloc[idx, 5:5 + 160].values
            video_x, clip_average_hr, ecg_label_new = self.get_single_video_x_aug(video_path, start_frame,
                                                                                  clip_average_hr, ecg_label)
        else:
            video_x = self.get_single_video_x(video_path, start_frame)
            ecg_label_new = self.landmarks_frame.iloc[idx, 5:5 + 160].values

        sample = {'video_x': video_x, 'frame_rate': frame_rate, 'ecg': ecg_label_new,
                  'clip_average_HR': clip_average_hr}

        if self.transform:
            sample = self.transform(sample)
        return sample

    def get_single_video_x(self, video_path, start_frame):
        video_jpgs_path = video_path

        video_x = np.zeros((clip_frames, 128, 128, 3))

        image_id = start_frame
        for i in range(clip_frames):

            image_name = 'face_' + str(image_id) + '.png'

            # face video
            image_path = os.path.join(video_jpgs_path, image_name)

            tmp_image = cv2.imread(image_path)

            if tmp_image is None:  # It seems some frames missing
                tmp_image = cv2.imread(self.root_dir + '20201016/1/face_1.png')

            tmp_image = cv2.resize(tmp_image, (128, 128), interpolation=cv2.INTER_CUBIC)

            video_x[i, :, :, :] = tmp_image

            image_id += 1

        return video_x

    def get_single_video_x_aug(self, video_path, start_frame, clip_average_hr, ecg_label):
        video_jpgs_path = video_path

        video_x = np.zeros((clip_frames, 128, 128, 3))
        ecg_label_new = np.zeros(clip_frames)

        if clip_average_hr > 88:
            clip_average_hr = clip_average_hr / 2
            for tt in range(clip_frames):
                if tt % 2 == 0:  # 0-158 → 0-79
                    image_id = start_frame + tt // 2

                    image_name = 'face_' + str(image_id) + '.png'
                    image_path = os.path.join(video_jpgs_path, image_name)
                    tmp_image = cv2.imread(image_path)
                    if tmp_image is None:  # It seems some frames missing
                        tmp_image = cv2.imread(self.root_dir + '20201016/1/face_1.png')
                    ecg_label_new[tt] = ecg_label[tt // 2]
                    tmp_image = cv2.resize(tmp_image, (128, 128), interpolation=cv2.INTER_CUBIC)

                else:
                    image_id1 = start_frame + tt // 2  # 0,1,2,3...79
                    image_id2 = image_id1 + 1  # 1,2,34...80

                    image_name = 'face_' + str(image_id1) + '.png'
                    image_path = os.path.join(video_jpgs_path, image_name)
                    tmp_image1 = cv2.imread(image_path)

                    image_name = 'face_' + str(image_id2) + '.png'
                    image_path = os.path.join(video_jpgs_path, image_name)
                    tmp_image2 = cv2.imread(image_path)

                    if tmp_image1 is None:  # It seems some frames missing
                        tmp_image1 = cv2.imread(self.root_dir + '20201016/1/face_1.png')
                    if tmp_image2 is None:  # It seems some frames missing
                        tmp_image2 = cv2.imread(self.root_dir + '20201016/1/face_1.png')

                    tmp_image1 = cv2.resize(tmp_image1, (128, 128), interpolation=cv2.INTER_CUBIC)
                    tmp_image2 = cv2.resize(tmp_image2, (128, 128), interpolation=cv2.INTER_CUBIC)
                    tmp_image = tmp_image1 // 2 + tmp_image2 // 2  # mean linear interpolation
                    ecg_label_new[tt] = ecg_label[tt // 2] / 2 + ecg_label[tt // 2 + 1] / 2

                image_x_aug = tmp_image
                video_x[tt, :, :, :] = image_x_aug

        else:  # double
            clip_average_hr = clip_average_hr * 2
            for tt in range(clip_frames):
                image_id = start_frame + tt * 2

                image_name = 'face_' + str(image_id) + '.png'
                image_path = os.path.join(video_jpgs_path, image_name)
                tmp_image = cv2.imread(image_path)
                if tmp_image is None:  # It seems some frames missing
                    tmp_image = cv2.imread(self.root_dir + '20201016/1/face_1.png')

                tmp_image = cv2.resize(tmp_image, (128, 128), interpolation=cv2.INTER_CUBIC)

                image_x_aug = tmp_image
                video_x[tt, :, :, :] = image_x_aug

                # approximation
                if tt < 80:
                    ecg_label_new[tt] = ecg_label[tt * 2]
                else:
                    ecg_label_new[tt] = ecg_label_new[tt - 80]

        return video_x, clip_average_hr, ecg_label_new
```

### 组内论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Deep Super-Resolution Network for rPPG Information Recovery and Noncontact Heart Rate Estimation | IEEE Transactions on Instrumentation and Measurement | Zijie Yue, Shuai Ding, Shanlin Yang, Hui Yang, Zhili Li, Youtao Zhang, Yinghui Li |  [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/abstract/document/9529062)  |  |
| CEBPM: A Cloud-Edge Collaborative Noncontact Blood Pressure Estimation Model | IEEE Transactions on Instrumentation and Measurement | Mengru Jia, YutingQin, Cheng Song, Zijie Yue, Shuai Ding |  [<i class="fa-solid fa-file"/>](https://ieeexplore.ieee.org/abstract/document/9885241/)  |  |