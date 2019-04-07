---
title: kubernetes中运行tensorflow
date: 2019-03-04 10:48:09
tags: 容器技术
categories: kubernetes
---

# Kubernetes中运行tensorflow



经过测试，目前搭建的`Kubernetes`测试集群已经可以运行`tensorflow`深度学习框架，本文档主要为总结运行测试过程及测试结果。关于`Kunernetes`集群环境搭建在此不做详细描述。



## 部署tensorflow

根据`Kubernetes`相关要求，`tensorflow`作为`Deployment`部署在集群环境中，并且本次测试`tensorflow`框架为`CPU`版本，关于`GPU`版本的测试，以后再做相关测试。`Kubernetes`已经可以支持`tensorflow`并行训练，在并行训练时，`tensorflow`容器分别配置为`ps`服务器及`worker`服务器，进行部署。



首先，需要编写`Deployment`相关配置文件，文件格式推荐使用`yaml`，也可以使用`json`格式编写，随后我会提供简单的`yaml2json`工具，方便将`yaml`格式文件转化为`json`格式，配置文件如下：

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: tensorflow-ps2
spec:
  replicas: 2
  template:
    metadata:
      labels:
        name: tensorflow-ps2
        role: ps
    spec:
      containers:
      - name: ps
        image: tensorflow/tensorflow:latest-py3
        ports:
        - containerPort: 2222
```

这里的部署选择的是`tensorflow`官方提供的`latest-py3`版本的框架。在`Kubernetes`集群的`Master`节点上使用`kubectl applf -f tensorflow.yml`。

完成后，使用`kubectl get deployment`查看部署的`Deployment`信息，输出结果如下：![get_deployment](get_deployment.png)

状态`AVAILABLE`下数量与`Deployment`配置文件中`replicas`数量一直，说明部署正常。

使用`kubectl get pod`命令查看部署的`Pod`:![get_pod](get_pod.png)

部署的两个`Pod`都处于`Running`状态，表明容器运行正常。

选择一个`Pod`名称，然后进入到容器内部，使用`kubectl exec -ti tensorflow-ps2-6c5688f54d-jq8v7 /bin/bash`:![exec_pod](exec_pod.png)

由于`pod`中网络使用的是`Kubernetes`组件中`coreDNS`和`kube-dns`提供的`DNS`解析服务，连接外部网络存在问题，这里可以在`Deployment`的配置文件中进行修改，为了完成测试，这里做一个临时配置方案，修改容器中`/etc/resolv.conf`文件，将`namserver`修改为`8.8.8.8`，然后修改`apt`的源为国内源，完成后，安装一些简单的工具：

```bash
apt-get update && apt-get upgrade
apt-get install -y vim
apt-get install -y wget
```

上述步骤完成后，使用`tensorflow`官方提供的脚本对`tensorflow`框架进行测试，脚本内容如下：

```python
import tensorflow as tf
mnist = tf.keras.datasets.mnist

(x_train, y_train),(x_test, y_test) = mnist.load_data()
x_train, x_test = x_train / 255.0, x_test / 255.0

model = tf.keras.models.Sequential([
  tf.keras.layers.Flatten(),
  tf.keras.layers.Dense(512, activation=tf.nn.relu),
  tf.keras.layers.Dropout(0.2),
  tf.keras.layers.Dense(10, activation=tf.nn.softmax)
])
model.compile(optimizer='adam',
              loss='sparse_categorical_crossentropy',
              metrics=['accuracy'])

model.fit(x_train, y_train, epochs=5)
model.evaluate(x_test, y_test)
```

调用脚本后，输出结果如下：![test_result](test_result.png)

完成上述步骤，证明`kubernetes`集群中运行`tensorflow`框架是可行的，关于`GPU`的调度，在最新版本的`kubernetes`中已提供支持，另外，共享存储等功能也可以集成在集群中，这部分功能暂时没有做过研究，后续会继续深入研究相关问题。

`kubernetes`官方提供的`kubeflow`是一个专门针对深度学习任务的集群插件，该插件功能暂时没有研究，根据描述，其设计思路与传统方案无太大异常，主要是提供了更为便捷的`API`以供使用。