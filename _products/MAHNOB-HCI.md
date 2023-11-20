---
title: MAHNOB-HCI
subtitle: 具有情感识别和隐式标签的多模态情感数据库
description: 这里是数据介绍
product_code: No.12132
layout: product
image: /assets/images/MAHNOB-HCI.png
price: 开放获取
author: Mohammad Soleymani et al.
date: 2011-08-04
rating: 4
features:
    -   label: 非接触式，心率检测，情感识别
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 秦玉婷维护
        icon: fa-id-card

---

MAHNOB-HCI最初为研究情感识别和隐式标签而构建的多模态数据集，后成为心率检测评估最常用的数据集之一。采集过程中安排27个受试者观看视频，同步记录32导EEG信号、外周生理信号（心电信号ECG、皮肤电信号GSR、呼吸信号、皮肤温度）、面部和身体视频（780×580@61fps）、眼睛凝视数据、音频数据，观看结束后受试者使用唤醒、效价、支配、可预测性以及情感关键词来自我报告他们所感受到的情感。

### 访问地址

[MAHNOB-HCI](https://mahnob-db.eu/hci-tagging/)

### 配置方式

1.ECG标签处理

```python
folderpath=r'/home/som/lab-data/seed-yzj/MAHNOB-HCI/Sessions/'
savepath = r'/home/som/lab/seed-yzj/paper4/MAHNOB_ECG12800detrend.csv'
write = open(savepath, 'a', newline='', encoding='gb18030')
writer = csv.writer(write)
falsefile=[]
sr=256

for i in range(2,3812,2):
    filepath=os.path.join(folderpath,"{0}".format(i))
    if os.path.exists(filepath)==False:
        continue

    bdfs=os.listdir(filepath)
    for j in range(len(bdfs)):
        if bdfs[j].split(".")[-1]=='bdf':
            datapath = os.path.join(filepath,bdfs[j])
            bdfRec = bdfRecording(datapath)
            rec = bdfRec.getData()
            ecgs = rec['data'][33, :]
            ecgs = ecgs[int(len(ecgs)/2-6400):int(len(ecgs)/2+6400)]
            
	    detrend_signals = detrend(ecgs, 100)  # 去趋势
            detrend_signals = detrend_signals.flatten()
            
            normalized_signals = normalize(detrend_signals)  # 标准化
            target_frequency = 50
            filter_signals = notch_filtering(target_frequency, 256, normalized_signals)  # 滤波
            recording = np.zeros(len(filter_signals) + 1)
            recording[0] = i
            recording[1:] = filter_signals
            writer.writerow(recording)

def LoadBDF(bdf_file, name="EXG2", start=None, end=None):
    with pyedflib.EdfReader(bdf_file) as f:
        status_index = f.getSignalLabels().index('Status')
        sample_frequency = f.samplefrequency(status_index)
        status_size = f.samples_in_file(status_index)
        status = np.zeros((status_size,), dtype='float64')
        f.readsignal(status_index, 0, status_size, status)
        status = status.round().astype('int')
        nz_status = status.nonzero()[0]

        video_start = nz_status[0]
        video_end = nz_status[-1]

        index = f.getSignalLabels().index(name)
        sample_frequency = f.samplefrequency(index)

        video_start_seconds = video_start / sample_frequency

        if start is not None:
            start += video_start_seconds
            start *= sample_frequency
            if start < video_start:
                start = video_start
            start = int(start)
        else:
            start = video_start

        if end is not None:
            end += video_start_seconds
            end *= sample_frequency
            if end > video_end:
                end = video_end
            end = int(end)
        else:
            end = video_end

        PhysicalMax = f.getPhysicalMaximum(index)
        PhysicalMin = f.getPhysicalMinimum(index)
        DigitalMax = f.getDigitalMaximum(index)
        DigitalMin = f.getDigitalMinimum(index)

        scale_factor = (PhysicalMax - PhysicalMin) / (DigitalMax - DigitalMin)
        dc = PhysicalMax - scale_factor * DigitalMax

        container = np.zeros((end - start,), dtype='float64')
        f.readsignal(index, start, end - start, container)
        container = container * scale_factor + dc

        return container, sample_frequency

def detrend(signals,param_lambda):
    # https://blog.csdn.net/piaoxuezhong/article/details/79211586
    signal_len = len(signals)
    I = np.identity(signal_len)
    B = np.array([1, -2, 1])
    ones = np.ones((signal_len - 2, 1))
    multi = B * ones
    D2 = np.zeros((signal_len - 2, signal_len))
    for i in range(D2.shape[0]):
        D2[i, i:i + 3] = multi[i]
    tr_D2 = np.transpose(D2)
    multi_D2 = np.dot(tr_D2, D2)
    inverse = I - (np.linalg.inv(I + (multi_D2 * pow(param_lambda, 2))))
    detrend_signals = np.dot(inverse, signals)
    return detrend_signals

def normalize(signals):
    normalized_signals = (signals - np.mean(signals)) / np.std(signals, ddof=1)
    return normalized_signals

def notch_filtering(target_frequency,frequency,signals):
    b, a = signal.iirnotch(target_frequency / (frequency / 2), 30)
    filtered_signals = signal.lfilter(b, a, signals)
    return filtered_signals
```

2.数据加载

```python
def load_all_data(ecgdatapath,imgsdatapath):
    labelarr= []
    ecgarr=[]
    timearr=[]
    frequencyarr=[]
    with open(ecgdatapath, 'r', encoding="utf-8") as csvfile:
        reader = csv.reader(csvfile)
        for row in reader:
            eachrow = [float(i) for i in row]
            labelarr.append(eachrow[-8])
            ecgarr.append(eachrow[1:-8])
            timedata,frequencydata=load_imgs(imgsdatapath,str(int(eachrow[0])))
            timearr.append(timedata)
            frequencyarr.append(frequencydata)

    labelarr=np.array(labelarr)
    ecgarr=np.array(ecgarr)
    timearr=np.array(timearr)
    frequencyarr=np.array(frequencyarr)
    return labelarr,ecgarr,timearr,frequencyarr

def load_imgs(imgspath,num):
    print(num)
    foreheadarr=[]
    nosearr=[]
    imgsfolder=imgspath+'/'+num
    imgs=os.listdir(imgsfolder)
    splitimglen=len(imgs)/2
    count = 0
    while True:
        i=int(splitimglen/2)-300

        foreheadimgpath = os.path.join(imgsfolder, 'forehead_{0}.jpg'.format(i))
        noseimgpath = os.path.join(imgsfolder, 'nose_{0}.jpg'.format(i))
        if count<600:
            if os.path.exists(foreheadimgpath):
                foreheadimg = image.load_img(foreheadimgpath, target_size=(7, 9))
                foreheadimg = image.img_to_array(foreheadimg)
                foreheadarr.append(foreheadimg)
                noseimg = image.load_img(noseimgpath,  target_size=(15, 31))
                noseimg = image.img_to_array(noseimg)
                nosearr.append(noseimg)
                # print('OK')
                count+=1
                i+=1
            else:
                i+=1
                continue
        else:
            break

    foreheadarr=np.array(foreheadarr)
    foreheadarr=foreheadarr.reshape((5,int(foreheadarr.shape[0]/5),foreheadarr.shape[1],foreheadarr.shape[2],foreheadarr.shape[3]))
    nosearr = np.array(nosearr)
    nosearr = nosearr.reshape((5, int(nosearr.shape[0] / 5), nosearr.shape[1], nosearr.shape[2], nosearr.shape[3]))
    timeseries=[]
    frequencyseries=[]
    for j in range(5):
        time3Ddimension,frequency3Ddimension=turntime_frequencysequence(foreheadarr[j],nosearr[j])
        timeseries.append(time3Ddimension)
        frequencyseries.append(frequency3Ddimension)
    timeseries=np.array(timeseries)
    frequencyseries=np.array(frequencyseries)

    return timeseries,frequencyseries
```

### 组内论文

| 题目   | 期刊 / 会议     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| Meta-rPPG: Remote Heart Rate Estimation Using a Transductive Meta-learner | ECCV 2020 | Eugene Lee et al. |  [<i class="fa-solid fa-file"/>](https://link.springer.com/chapter/10.1007/978-3-030-58583-9_24)  | [<i class="fa-brands fa-github"/>](https://github.com/eugenelet/Meta-rPPG) |
| PhysFormer: Facial Video-based Physiological Measurement with Temporal Difference Transformer | CVPR 2022 | Zitong Yu et al. |  [<i class="fa-solid fa-file"/>](https://openaccess.thecvf.com/content/CVPR2022/html/Yu_PhysFormer_Facial_Video-Based_Physiological_Measurement_With_Temporal_Difference_Transformer_CVPR_2022_paper.html)  | [<i class="fa-brands fa-github"/>](https://github.com/ZitongYu/PhysFormer)  |  |