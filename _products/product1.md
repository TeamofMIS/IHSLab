---
title: ImageNet
subtitle: 由斯坦福提出的影像数据集
description: 这里是数据介绍
product_code: No.12131
layout: product
image: https://cs.stanford.edu/people/karpathy/cnnembed/cnn_embed_full_1k.jpg
price: 公开
author: 李飞飞
date: 2023-01-01
rating: 4
features:
    -   label: 多模态
        icon: fa-file-archive
    -   label: 自定义协议，非商业可用
        icon: fa-copyright
    -   label: Big-data节点
        icon: fa-desktop
    -   label: 张三维护
        icon: fa-id-card

---

ImageNet数据集是一个用于视觉对象识别软件研究的大型可视化数据库，由李飞飞团队从2007年开始，耗费大量人力，通过各种方式（网络抓取，人工标注，亚马逊众包平台）收集制作而成。它作为论文在CVPR-2009发布，自2010年以来，ImageNet项目每年举办一次软件比赛，即ImageNet大规模视觉识别挑战赛（ImageNet
Large Scale Visual Recognition Challenge，ILSVRC）。

ImageNet数据集的目标是识别和分类图像中的各种物体和场景。它包含了超过1400万张图像，涵盖了从动物到交通工具等各个领域的图像。这些图像都被精确地标注了物体类别和位置标签，使得该数据集在训练和评估算法时，能够提供准确的参考标准。然而，该数据集中的一些类别具有很高的相似性，使得图像分类任务变得更加具有挑战性。

ImageNet数据集在计算机视觉领域有着广泛的应用，包括但不限于：

图像分类：通过使用大量的标注图像，可以训练出准确的分类模型，用于自动识别和分类图像中的物体和场景。
目标检测：通过在图像中标注物体的位置和类别信息，可以训练出能够自动检测图像中物体的算法模型。
图像生成：通过学习ImageNet数据集中的图像分布，可以生成逼真的图像样本。

### 访问地址

[ImageNet](https://www.image-net.org/index.php)

### 配置方式

```python
import tensorflow.compat.v2 as tf


class Lion(tf.keras.optimizers.legacy.Optimizer):
  r"""Optimizer that implements the Lion algorithm."""

  def __init__(self,
               learning_rate=0.0001,
               beta_1=0.9,
               beta_2=0.99,
               wd=0,
               name='lion',
               **kwargs):
    """Construct a new Lion optimizer."""

    super(Lion, self).__init__(name, **kwargs)
    self._set_hyper('learning_rate', kwargs.get('lr', learning_rate))
    self._set_hyper('beta_1', beta_1)
    self._set_hyper('beta_2', beta_2)
    self._set_hyper('wd', wd)

  def _create_slots(self, var_list):
    # Create slots for the first and second moments.
    # Separate for-loops to respect the ordering of slot variables from v1.
    for var in var_list:
      self.add_slot(var, 'm')

  def _prepare_local(self, var_device, var_dtype, apply_state):
    super(Lion, self)._prepare_local(var_device, var_dtype, apply_state)

    beta_1_t = tf.identity(self._get_hyper('beta_1', var_dtype))
    beta_2_t = tf.identity(self._get_hyper('beta_2', var_dtype))
    wd_t = tf.identity(self._get_hyper('wd', var_dtype))
    lr = apply_state[(var_device, var_dtype)]['lr_t']
    apply_state[(var_device, var_dtype)].update(
        dict(
            lr=lr,
            beta_1_t=beta_1_t,
            one_minus_beta_1_t=1 - beta_1_t,
            beta_2_t=beta_2_t,
            one_minus_beta_2_t=1 - beta_2_t,
            wd_t=wd_t))

  @tf.function(jit_compile=True)
  def _resource_apply_dense(self, grad, var, apply_state=None):
    var_device, var_dtype = var.device, var.dtype.base_dtype
    coefficients = ((apply_state or {}).get((var_device, var_dtype)) or
                    self._fallback_apply_state(var_device, var_dtype))

    m = self.get_slot(var, 'm')
    var_t = var.assign_sub(
        coefficients['lr_t'] *
        (tf.math.sign(m * coefficients['beta_1_t'] +
                      grad * coefficients['one_minus_beta_1_t']) +
         var * coefficients['wd_t']))
    with tf.control_dependencies([var_t]):
      m.assign(m * coefficients['beta_2_t'] +
               grad * coefficients['one_minus_beta_2_t'])

  @tf.function(jit_compile=True)
  def _resource_apply_sparse(self, grad, var, indices, apply_state=None):
    var_device, var_dtype = var.device, var.dtype.base_dtype
    coefficients = ((apply_state or {}).get((var_device, var_dtype)) or
                    self._fallback_apply_state(var_device, var_dtype))

    m = self.get_slot(var, 'm')
    m_t = m.assign(m * coefficients['beta_1_t'])
    m_scaled_g_values = grad * coefficients['one_minus_beta_1_t']
    m_t = m_t.scatter_add(tf.IndexedSlices(m_scaled_g_values, indices))
    var_t = var.assign_sub(coefficients['lr'] *
                           (tf.math.sign(m_t) + var * coefficients['wd_t']))

    with tf.control_dependencies([var_t]):
      m_t = m_t.scatter_add(tf.IndexedSlices(-m_scaled_g_values, indices))
      m_t = m_t.assign(m_t * coefficients['beta_2_t'] /
                       coefficients['beta_1_t'])
      m_scaled_g_values = grad * coefficients['one_minus_beta_2_t']
      m_t.scatter_add(tf.IndexedSlices(m_scaled_g_values, indices))

  def get_config(self):
    config = super(Lion, self).get_config()
    config.update({
        'learning_rate': self._serialize_hyperparameter('learning_rate'),
        'beta_1': self._serialize_hyperparameter('beta_1'),
        'beta_2': self._serialize_hyperparameter('beta_2'),
        'wd': self._serialize_hyperparameter('wd'),
    })
    return config
```

### Benchmarks

| 任务                         | 数据集变体         | 最优模型        | 代码                                                     | 论文                                                                |
|----------------------------|---------------|-------------|--------------------------------------------------------|-------------------------------------------------------------------|
| Image Classification       | ImageNet      | BASIC-L     | [<i class="fa-brands fa-github"/>](https://github.com) | [<i class="fa-solid fa-file"/>](https://arxiv.org/abs/2302.06675) |
| Neural Architecture Search | ImageNet      | DeepMAD-50M | [<i class="fa-brands fa-github"/>](https://github.com) | [<i class="fa-solid fa-file"/>](https://arxiv.org/abs/2302.06675) |
| Long-tail Learning         | ImageNet+LT   | $\mu$Net+   | [<i class="fa-brands fa-github"/>](https://github.com) | [<i class="fa-solid fa-file"/>](https://arxiv.org/abs/2302.06675) |
| Image Generation           | ImageNet Real | RIN         | [<i class="fa-brands fa-github"/>](https://github.com) | [<i class="fa-solid fa-file"/>](https://arxiv.org/abs/2302.06675) |

### 小组论文

| 题目   | 期刊     | 作者  | 地址 | 代码                                                     |
|------|--------|-----|----|--------------------------------------------------------|
| XXXX | Nature | XXX |    | [<i class="fa-brands fa-github"/>](https://github.com) |
| XXXX | Nature | XXX |    | [<i class="fa-brands fa-github"/>](https://github.com) |
| XXXX | Nature | XXX |    | [<i class="fa-brands fa-github"/>](https://github.com) |




