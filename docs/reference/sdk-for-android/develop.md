

# 开始开发

<LastUpdated/>

## 第一步：新建 Android 工程

![](./images/create_project1.png)

![image-20220327173229220](./images/create_project2.png)



## 第二步：添加 Guard 依赖

```groovy
implementation 'cn.authing:guard:+'
```



## 第三步：初始化

在应用启动时调用：

```java
Authing.init(appContext, "your_authing_app_id");
```



接下来，应用程序只需要 1 分钟，即可嵌入 Authing 提供的标准认证流程和界面。



<span style="background-color: #396aff;a:link:color:#FFF;padding:8px;border-radius: 4px;"><a href="./quick.html" style="color:#FFF;">快速接入 →</a>
</span>

