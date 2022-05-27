# 使用TensorFlow Lite Model Maker生成图像分类器模型

- [使用TensorFlow Lite Model Maker生成图像分类器模型](#使用tensorflow-lite-model-maker生成图像分类器模型)
  - [预备工作](#预备工作)
  - [模型训练](#模型训练)
    - [获取数据](#获取数据)
    - [运行示例](#运行示例)



## 预备工作

首先安装程序运行必备的一些库。

**由于安装的库比较多，所以可以选择同时安装多个第三方包的方式进行安装，以节省时间。**

步骤如下：

新建一个requirements.txt文件，写入以下内容：

```txt
tflite-model-maker
conda-repo-cli==1.0.4
anaconda-project==0.10.1
```

然后使用anaconda跳转到txt文件路径，执行以下命令：

```conda
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple   -r requirements.txt
```

然后等待安装完成即可。



然后到jupyter导入相关库：

```python
import os

import numpy as np

import tensorflow as tf
assert tf.__version__.startswith('2')

from tflite_model_maker import model_spec
from tflite_model_maker import image_classifier
from tflite_model_maker.config import ExportFormat
from tflite_model_maker.config import QuantizationConfig
from tflite_model_maker.image_classifier import DataLoader

import matplotlib.pyplot as plt

```

## 模型训练

### 获取数据

本实验先从较小的[数据集](https://so.csdn.net/so/search?q=数据集&spm=1001.2101.3001.7020)开始训练，当然越多的数据，模型精度更高。


```python
image_path = tf.keras.utils.get_file(
      'flower_photos.tgz',
      'https://storage.googleapis.com/download.tensorflow.org/example_images/flower_photos.tgz',
      extract=True)
image_path = os.path.join(os.path.dirname(image_path), 'flower_photos')

```

这里从`storage.googleapis.com`中下载了本实验所需要的数据集。`image_path`可以定制，默认是在用户目录的`.keras\datasets`中。

### 运行示例

第一步：加载数据集，并将数据集分为训练数据和测试数据。


```python
data = DataLoader.from_folder(image_path)
train_data, test_data = data.split(0.9)

```

    INFO:tensorflow:Load image with size: 3670, num_label: 5, labels: daisy, dandelion, roses, sunflowers, tulips.

第二步：训练[Tensorflow](https://so.csdn.net/so/search?q=Tensorflow&spm=1001.2101.3001.7020)模型

```python
# model = image_classifier.create(train_data)



inception_v3_spec = image_classifier.ModelSpec(uri='D:\Workspace\JupyterNotebookFiles\E5\efficientnet_lite0_feature-vector_2')


inception_v3_spec.input_image_shape = [240, 240]
model = image_classifier.create(train_data, model_spec=inception_v3_spec)
```

    INFO:tensorflow:Retraining the models...


    INFO:tensorflow:Retraining the models...


    Model: "sequential"
    _________________________________________________________________
     Layer (type)                Output Shape              Param #   
    =================================================================
     hub_keras_layer_v1v2 (HubKe  (None, 1280)             3413024   
     rasLayerV1V2)                                                   
                                                                     
     dropout (Dropout)           (None, 1280)              0         
                                                                     
     dense (Dense)               (None, 5)                 6405      
                                                                     
    =================================================================
    Total params: 3,419,429
    Trainable params: 6,405
    Non-trainable params: 3,413,024
    _________________________________________________________________
    None
    Epoch 1/5


    E:\Anaconda3\lib\site-packages\keras\optimizers\optimizer_v2\gradient_descent.py:108: UserWarning: The `lr` argument is deprecated, use `learning_rate` instead.
      super(SGD, self).__init__(name, **kwargs)


    103/103 [==============================] - 59s 548ms/step - loss: 0.8704 - accuracy: 0.7706
    Epoch 2/5
    103/103 [==============================] - 60s 582ms/step - loss: 0.6518 - accuracy: 0.8968
    Epoch 3/5
    103/103 [==============================] - 59s 563ms/step - loss: 0.6229 - accuracy: 0.9166
    Epoch 4/5
    103/103 [==============================] - 65s 632ms/step - loss: 0.6045 - accuracy: 0.9269
    Epoch 5/5
    103/103 [==============================] - 61s 591ms/step - loss: 0.5897 - accuracy: 0.9345

第三步：评估模型

```python
loss, accuracy = model.evaluate(test_data)

```

    12/12 [==============================] - 8s 478ms/step - loss: 0.6278 - accuracy: 0.9074

第四步，导出Tensorflow Lite模型

```python
model.export(export_dir='.')
```

    INFO:tensorflow:Assets written to: C:\Users\qiaoqiao\AppData\Local\Temp\tmphfpb_ks9\assets


    INFO:tensorflow:Assets written to: C:\Users\qiaoqiao\AppData\Local\Temp\tmphfpb_ks9\assets
    E:\Anaconda3\lib\site-packages\tensorflow\lite\python\convert.py:766: UserWarning: Statistics for quantized inputs were expected, but not specified; continuing anyway.
      warnings.warn("Statistics for quantized inputs were expected, but not "





