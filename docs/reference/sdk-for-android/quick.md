# 快速接入

<LastUpdated/>

通过 Authing 提供的标准认证流程和界面，应用程序可以 1 分钟完成接入。

首先确保已经完成了 开发准备工作，然后在需要认证的地方调用：

```java
AuthFlow.start(this);
```

效果如下：

<img src="./images/standard.png" alt="image-20220407122556503" style="zoom:67%;" />

如果应用需要自定义认证流程和界面，推荐使用超组件（Hyper Component）快速构建认证流程和界面。


