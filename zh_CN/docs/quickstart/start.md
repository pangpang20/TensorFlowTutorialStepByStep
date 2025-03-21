# 适合初学者的快速入门

介绍使用 Keras 进行以下操作：

1) 加载一个预构建的数据集。
2) 构建对图像进行分类的神经网络机器学习模型。
3) 训练此神经网络。
4) 评估模型的准确率。

## 设置TensorFlow  
激活python虚拟环境

```bash
source mytf/bin/activate
python

```

导入TensorFlow 

```python
import tensorflow as tf
```

## 加载数据集

MNIST（Modified National Institute of Standards and Technology）数据集是机器学习领域最著名和使用最广泛的数据集之一，被誉为机器学习的"Hello World"。

数据集中的内容是手写数字的图片（0-9），每张图片的大小为28x28像素，像素点的灰度范围是0-255，图形已经经过大小归一化和中心对齐处理。

一共包含70000张图片，其中60000张用于训练，10000张用于测试。

数据被分为特征（图像）和标签（对应的数字）。

MNIST的优点是数据集很小，便于初学者快速上手。缺点是数据集相对简单，现代深度学习模型容易达到高准确率不包含彩色图像或复杂背景数据量相对较小（按现代标准）。

通过 `tf.keras.datasets.mnist` 加载 MNIST 数据集。

将样本数据进行归一化处理（除以255.0）将像素值缩放到0-1范围。


```python
mnist = tf.keras.datasets.mnist

(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0

```

## 构建机器学习模型

通过堆叠层来构建 tf.keras.Sequential 模型。

堆叠层是指在构建神经网络时，将多个神经网络层按顺序叠加在一起形成网络结构的方法。在TensorFlow中，特别是使用Keras API时，可以通过tf.keras.Sequential模型来实现层的堆叠。

堆叠层创建了一个线性的层次结构，数据按顺序从输入层流向输出层，每一层处理前一层的输出并将结果传递给下一层。

每一层都是一个独立的处理单元，具有自己的权重和激活函数，可以单独配置。

信息在网络中的流动是单向的，从输入到输出按照层的堆叠顺序依次处理。

堆叠层的优势：容易理解和实现、结构清晰便于优化，可以轻松地添加、删除或替换层，便于进行实验和调整。

```python
model = tf.keras.models.Sequential([
  tf.keras.layers.Flatten(input_shape=(28, 28)),
  tf.keras.layers.Dense(128, activation='relu'),
  tf.keras.layers.Dropout(0.2),
  tf.keras.layers.Dense(10)
])

```

对于每个样本，模型都会返回一个包含 logits 或 log-odds 分数的向量，每个类一个分数。

```python
predictions = model(x_train[:1]).numpy()
predictions 
```
> array([[ 1.0593462 ,  1.3974222 ,  0.41937485,  0.16066086, -0.22344418,
        -0.32328215, -0.13380559,  0.19494104, -0.49787784, -0.7295142 ]],
      dtype=float32)

tf.nn.softmax 函数将这些 logits 转换为每个类的概率。

```python
tf.nn.softmax(predictions).numpy()

```

使用 losses.SparseCategoricalCrossentropy 为训练定义损失函数，它会接受 logits 向量和 True 索引，并为每个样本返回一个标量损失。

```python
loss_fn = tf.keras.losses.SparseCategoricalCrossentropy(from_logits=True)

```

损失等于 true 类的负对数概率：如果模型确定类正确，则损失为零。

```python
loss_fn(y_train[:1], predictions).numpy()
model.compile(optimizer='adam',
              loss=loss_fn,
              metrics=['accuracy'])
```

## 训练并评估模型

使用 Model.fit 方法调整模型参数并最小化损失。

``` python
model.fit(x_train, y_train, epochs=5)

```

Model.evaluate 方法通常在 "Validation-set" 或 "Test-set" 上检查模型性能。

```python
# 使用测试数据集 (x_test, y_test) 计算模型在测试集上的误差
model.evaluate(x_test,  y_test, verbose=2)
```
第一个值是损失，第二个值是准确率。

损失值越低，准确率越高，模型表现越好。

> [0.07920240610837936, 0.9749000072479248]

表示：
0.079 → 模型在测试集上的损失值（误差）

97.5% → 模型在测试集上的准确率
