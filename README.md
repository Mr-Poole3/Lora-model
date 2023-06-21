<h3>本教程已上传视频教程于哔哩哔哩<br>
网址：https://www.bilibili.com/video/BV1ah411K7Jp/
<br>
# Stable Diffusion模型训练

## 训练一个属于自己的模型

前面介绍了那么多，相信你一定对Stable Diffusion有了一定的认知，你是否也在想：我下载的都是别人训练的模型，那我该怎么样得到一个属于我自己风格的模型呢？

## 安装前的准备

#### 1.Kohya_ss GUI安装

 对于我们普通消费级电脑来说，在所有的模型训练中，似乎只有LoRA可以利用少量的数据进行训练了，而Kohya_ss GUI则是一个非常方便的LoRA训练的平台。

#### 2.conda创建python虚拟环境

 ![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/e39f6f10-1268-4667-a1f5-57fd39d1367e)


​	clone项目并下载依赖

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/7a2772f7-24dc-4590-8cef-64bfe43cebdd)


![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/a482b795-9362-41c7-98dc-784a79164cd6)

​		输入：

```
git reset --hard 3633e1afc7bffbe61957f04e7bb1a742ee910ace
```

#### 3.在sd-scripts同级目录安装kohya_ss

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/04f8a1d0-8ad8-4706-9c02-6b35ed036205)

#### 4.点击gui.bat或者gui-user.bat以启动koyya_ss<br>![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/0ae1e34a-74a4-4d80-81d5-e12896bf5369)


#### 5.老样子，下载好所有的依赖和环境后，点击网址进入web界面。

## 模型训练准备

准备你想训练的风格或者人物，比如我准备训练一个迪丽热巴的真人写实模型，先从网上下载有关迪丽热巴的照片（真人模型至少70张，动漫20张）保存到新建文件夹1_in里

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/c85a1188-e1ef-46a0-8b7b-fbad0552cdab)


要求图片清晰，背景单一，突出人物主体，统一剪裁到512*512的（太大训练时显卡给你烧了）

<!--在1_in的同级文件夹里新建1_out，log，model用于储存。-->

返回到先前的Stable Diffusion对图像进行标签处理

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/23a08530-90ca-425d-abc5-cf08ee87615c)


处理完成后，1_out里面每一张图片会多了一个.text文件，这是对图像打的标签。

打开文件之后，可以看到类似的文字描述“dilireba a woman in a black dress with a red lipstick”。作为简单的练手，我们不需要修改任何东西。如果你要提升效果，可以手动加入更加详细，更加精准的描述。

打开kohya_ss

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/b421ccf3-dc45-43d8-b656-47eb0e529651)


先将④处的模型快速选择（Model Quick Pick）设置成自定义（Custom），这里也可以用预设的V1.5,V2.1。但是使用这些预设模型，会需要很长的时间在线下载，而且会占据巨大的C盘空间，不是很推荐。

然后在左边③这里选择具体的本地模型，我这里用的是适合亚洲人的chilloutmix模型（需要自己下载）。通过点击输入框后面的文件图标，找到具体的模型文件就可以了。

然后右边⑤处的模型保存格式选择safetensors。

#### 文件夹设置

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/05f38c9a-4bb8-49a1-869f-7c214b44e554)


#### 参数调节（新手建议默认）

以下是我的参数（显卡差点儿爆炸，所以真心建议默认）

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/681f4b1d-990c-4203-8847-6f105427f053)


开始训练！！！！

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/50b77c33-3b73-438f-b71d-3eb05e6d3d6b)


<!--我的3060 laptop训练了大概5小时-->

## 导入模型

搞了那么久终于可以用了。lora模型的使用，我们之前的文章里面已经有详细的介绍了，这里就简单的演示一下。当lora训练结束之后，会在对应的model文件下面生成模型文件。比如下图

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/86abf699-c3b9-45de-abb2-385cc399895a)


![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/2fb99877-6ad5-4c9d-9a9e-be31880cf23c)


然后选择好模型，输入关键词，选择对应的lora，然后点击生成即可，刚开始不要加任何复杂的关键词，只用最基础的，比如 "a woman dilireba" 然后加上我们自己训练lora。关于Lora字符串，你可以直接输入，也可以通过图中圆圈处找到对应的Lora点击一下导入。导入的时候最右侧的参数默认为1，需要修改一下，改成0.7或者0.8。

如果Lora练的还可以，这个时候出来的图片应该是相识度比较高的。然后就可以在这个基础词语上做一些变化了，比如加一个“wearing a suit” （穿着西装）就可以得到下面的图片了。

![image](https://github.com/Mr-Poole3/Lora-model/assets/112788987/06af114e-57d1-4f3a-a86e-7c5cb7f3fefd)

<br>关于如何安装Stable Diffusion可以参考我的另一篇文章：https://github.com/Mr-Poole3/Stable-Diffusion
<br>
再次感谢<br>https://github.com/Stability-AI/StableDiffusion<br>
https://github.com/OpenTalker/SadTalker<br>
https://github.com/bmaltais/kohya_ss<br>
项目的开源！！！
