[TOC]

### 摘要

#### 中文

智能手机已经成为日常生活中必不可少的设备, 为了解决用户隐私、财产等安全性问题，为各大手机厂商推出了各种解锁方式。目前较为流行的解锁方式是指纹识别和人脸识别，比如苹果公司的FaceID、TouchID等。但是再严密的系统也是有漏洞的，人脸识别可以用“人脸仿真面具”破解，指纹识别可以用硅胶手套破解。因此，我们想设计出一种更加先进的解锁方式，来提高安全性。我们提出了一种基于超声波的身份认证系统，可以同时采集用户特定行为的动作和声音两个模态的特征，再将这两种特征一起放入深度学习模型中计算，最终判断出是否是用户本人在解锁手机。由于它比目前流行的人脸解锁、指纹解锁多了一个模态的特征，因此安全性会更高，用户身份更难被伪造。本文的主要研究内容有如下几点：

* 研究了一种基于时域信号进行动作识别的方法，能够分别从有声频段和超声频段二者中提取特征。

* 提出了一种能够利用提取的特征来进行身份识别的深度学习模型。这种深度学习模型在one shot问题和five shot问题上准确率分别是93.8%和97.5%。

* 提出了一种能够将有声频段和超声频段结合起来分辨的方法，能够让深度学习模型自行学得分配权重。

  

**关键词：多模态、超声波、身份认证**



#### English

Smartphones have become an indispensable device in daily life. In order to solve security problems such as user privacy and property, various unlocking methods have been introduced by major mobile phone manufacturers. Currently, the most popular unlocking methods are fingerprint recognition and face recognition, such as Apple's FaceID and TouchID. But no matter how tight the system is, there are still loopholes. For instance, face recognition can be cracked with a technology called "3D Face Simulation Mask", and fingerprint recognition can be cracked with silicone gloves. Therefore, we want to design a more advanced unlocking method to improve security. We intorduce an ultrasonic-based identity authentication system, which can simultaneously collect the features of two modalities: the action and voice of user-specific behavior. Then we put these two features together into the deep learning model for calculation, and finally the model will determine whether the user himself is unlocking the phone. Because it has one more modal feature than the currently popular face unlocking and fingerprint unlocking, the security will be higher, and the user identity will be more difficult to forge.The main research contents of this paper are as follows:

* Researched a method for action recognition based on time-domain signals, which can extract features from both the audio frequency band and the ultrasonic frequency band.
* Proposed a deep learning model capable of utilizing the extracted features for identity recognition. This deep learning model achieves 93.8% and 97.5% accuracy on one shot and five shot problems, respectively.
* Proposed a method that can combine the audio frequency band and the ultrasonic frequency band to distinguish, which allows the deep learning model to learn to assign weights by itself.

**Keywords: Multimodal, Ultrasonic, Identity Authentication**



### 1. 引言

#### 1.1 研究背景及意义

随着现代科技的进步，智能手机已经成了日常生活中必不可少的设备，人们使用智能手机的场景越来越多。现在几乎每个地方都少不了智能手机的身影，比如到了消费场所我们用手机支付，到了旅游胜地我们用手机拍照，就算是平时坐在咖啡厅我们也会用手机聊天。可见智能手机对于我们来说已经是必不可少的了，为了方便我们把各种资料、个人信息甚至财产都交给了手机保管，为了走在路上都能随时调出来使用。但是这样做也会导致我们的个人信息泄露、财产丢失这些问题发生的可能性飙升。为了解决这个问题，各大手机厂商、手机操作系统都推出了各种解锁方式，目前最为流行的是指纹识别、人脸识别了。然而就算是这种严密的识别技术，黑客们还是能破解掉进入用户的手机，获取他们的个人信息，窃取他们的财产。比如，对于苹果手机的Face id解锁技术，早在2019年就有人用“3D人脸仿真面具”破解了；对于目前最流行的指纹识别技术，只要使用带有用户指纹的硅胶手套也一样能够轻松解锁手机。此外，这些解锁技术无外乎都是利用几乎是每个人独有的特征来进行身份认证的，一旦这种足以代表个人身份的信息泄露（人脸识别的照片、指纹识别的指纹），可能会带来严重的后果，甚至还会遭到恶性骚扰、人肉搜索等威胁事件。

因此我们希望研究出一种全新的身份认证方式来保护我们的智能设备。这种方式使用的判断标准不仅不易被伪造，而且不应依赖于用户较为隐私的信息。这样即使认证信息被泄露也不会导威胁到用户本人。我们知道每个人都有自己特有的行为特征，这些特征习惯虽然我们自己平时生活中发现不到，但是都在影响着我们。根据这个特点，我们决定通过用户做出特定动作时的行为特征再通过做特定动作是特有的音色来判断是不是用户本人。实验结果表明，这种认证方式是可行的，计算机确实可以检测出不同用户在做同一个动作时候的运动轨迹和声音特征的细小差异。

#### 1.2 相关工作及研究现状

##### 1.2.1 基于声音的身份认证

基于声音的身份认证又叫声纹识别。声纹不仅具有特定性，而且有相对稳定性的特点。成年以后，人的声音可保持长期相对稳定不变，因此，它同指纹一样，独特的生物学特征，可用于身份识别。它通过对于声音数据的预处理，提取声音的特征，然后将其放入机器学习或者深度学习的模型中运算，最后进行相似度打分得出结果，判断出该说话人是不是用户本身，或者从多个用户中判断出哪个用户是说话人。目前较为流行的声音特征有MFCC、D-vector、bottleneck等，我们在有声频段选择提取的特征就是MFCC特征。但是声纹识别对0-18岁的孩子是不友好的，因为这个年龄段的孩子的声音相对成年人来说不够稳定。另外，声音信息十分容易被伪造，安全性相对于人脸和指纹并不是很高，关于安全性算法方面还有待提升。

##### 1.2.2 基于超声波的动作识别

基于超声波的动作识别主要是依靠声音的多普勒效应。由设备发出22khz的超声波，当用户做出动作时候就会产生多普勒效应，导致设备麦克风录制的声音除了22khz超声波的回声，还会有其他频率的声音返回。我们可将STFT提取出的频谱图或者原始STFT数据放入模型中，判断出该用户在做什么动作。曾经有很多研究者就是基于这个原理，可以读取说话者嘴唇的运动分析出说话的内容。这种检测动作的方法可以在用户毫无感知的情况下检测出来，不仅不太占用手机有限的空间大小，还能达到一定的准确度。但是只基于超声波的动作检测虽然可以识别出不同的动作，但是对于不同的人的同一个动作并不能达到很高的准确度。因此我们需要将超声部分和有声部分结合起来，才能达到较高的准确率和较低的错误拒绝率。

#### 1.3 本文研究内容

在本文中，我们提出了一种基于超声波的身份认证系统。我们在预处理时提取出有声频段和超声频段两个特征，将二者放入各自的深度学习模型中计算出相应的更深层的特征，最后将两个模态的特征放到辨别器中计算和用户原动作特征的相似度，判断是不是用户本人。具体内容如下：

1. 利用手机扬声器发出22khz的超声波，实验者进行打响指动作，再由麦克风收集回声以及打响指的声音。

2. 利用傅里叶变换，将声音数据从原始的时域数据转换成频域数据。然后分理处有声频段和超声频段。对于有声频段使用MFCC提取特征，对于超声频段使用STFT提取特征。
3. 构建深度学习模型，实验证明有声频段使用LSTM，超声频段使用ResNet准确度和错误拒绝率都有很好的表现。
4. 将深度学习模型提取出来的两个模态的特征向量拼接起来放入全连接层，映射到一个单元中使用sigmoid函数计算出该身份是否是用户本人的概率。

### 2. 相关原理和技术

#### 2.1声音信号的分析

##### 2.1.1 多普勒效应

多普勒效应是身份认证系统中动作检测的主要原理之一。多普勒效应（Doppler effect）是波源和观察者有相对运动时，观察者接受到波的频率与波源发出的频率并不相同的现象。观察者接收到的频率具体计算如公式（2.1）：
$$
f'=(\frac{v\pm v_0}{v \mp v_s})f \tag{2.1}
$$

其中f‘是观察者接受的频率；f是波源的频率；v为波在该介质中的行进速度；v<sub>0</sub>为观察者相对于介质的移动速度，若接近发射源则前方运算符号为+号，反之则为−号；v<sub>s</sub>为发射源相对于介质的移动速度，若接近观察者则前方运算符号为−号，反之则为+号。在实验中，实验者在手机周围打响指，由于手部在相对于手机一直在运动，就会产生多普勒频移（如图1所示）。我们如果能分析出这段时间内麦克风收集的声音的频率变化，就可以获取超声频段的特征了。

<img src="https://s2.loli.net/2022/03/25/aI6CfSM78rde9D3.png" title="图片title" style="zoom: 80%;" />

（图1：该图为超声频段经STFT绘制出的频谱图，上面是没有动作时的频率变化，下面是手部上下移动时的频率变化）

##### 2.1.2 特征提取

1. STFT（Short-Time Fourier Transform，短时傅里叶变换），是和傅里叶变换相关的一种数学变换，用以确定时变信号其局部区域正弦波的频率与相位。我们需要获得频率随时间变化的特征，短时傅里叶变换就是将原来的傅里叶变换在时域截短为多段分别进行傅里叶变换，每一段记为时刻t<sub>i</sub>，对应FFT求出频域特性，就可以粗略估计出时t<sub>i</sub>时的频域特性了。截断信号的工具称为窗函数（如图2所示），窗越小，时域特性越明显，但是此时由于点数过少导致FFT降低了精确度，导致频域特性不明显。另外，为了保证频域特性的基础上提高时域特性，经常选择前后窗函数重叠一部分（如图2所示），这样两个窗确定的时刻就比较接近就提高了时域分析能力。但不是重叠越多越好，重叠点数过多会大幅增加计算量，导致效率低下，因此前后窗重叠的点数也需要外加确定。



![](https://s2.loli.net/2022/03/25/XfOGpJ4DCarEFkW.png)

（图2：overlap即表示重叠的部分，方框表示的是窗函数，对截取的信号与对应窗相乘，再做N点DFT）

2. MFCC（Mel-Frequency Cepstral Coefficients，梅尔频率倒谱系数），是一种常用的声音特征提取的算法。它考虑到了人类的听觉特征，先将线性频谱映射到基于听觉感知的Mel非线性频谱中，然后转换到倒谱上。即将频谱通过一组Mel滤波器就得到Mel频谱，公式表述如公式（2.2），这时候我们再在log X[k]上进行倒谱分析，在Mel频谱上面获得的倒谱系数h[k]就称为Mel频率倒谱系数，简称MFCC。

$$
\log X[k] = \log (Mel-Spectrum) \tag {2.2}
$$

#### 2.2 深度学习模型

深度学习模型是用来将上述预处理中提取的STFT和MFCC特征再次进行非线性变换，重新改变特征向量的维度，获取更深层次的特征。根据两种模态的信息特征，我们可以知道，MFCC和STFT的原始数据都具有时间特征，是一种序列性的数据；而如果将二者的特征绘制成图片的话，那便是一种图片信息。根据多次模型组合实验后，我们最终选择采用的模型是LSTM和Res50两个模型（模型结构如图3）。其中LSTM是RNN中的一种架构，Res50是基于残差块的一种CNN结构。LSTM用于有声频段即MFCC特征的处理，Res50用于超声频段即STFT特征的处理。

<img src="https://s2.loli.net/2022/03/25/vDoeYz1JKL8i3QN.png" title="图片title" style="zoom: 60%;" />

（图3：左边是LSTM基本结构，右边是Res50每个残差块的结构和参数）

### 3. 实现方法和技术

#### 3.1数据处理

##### 3.1.1预处理

我们制作了一个长达10分钟的22khz频率的超声波音频信号。然后实验者使用实验手机的扬声器播放该音频，以每三秒一次的频率做打响指动作，同时打开手机的麦克风进行声音的录制。录制完声音后，将其切割成三秒一个的wav文件，每个wav文件包含一次响指的动作。由于数据收集过程繁琐复杂，导致我们数据量不是很足，因此后期我们进行了数据增强操作，对音频加入了权重为0.0003的高斯白噪声，并且进行了在0.6到1.4之间的随机倍速调整。最终训练集的规模达到了2000个音频文件，如果一对一组合的话，理论上可以生成400万对训练数据。

##### 3.1.2特征提取

收集完数据样本后，通过傅里叶变换将将其从时域信号转换到频域信号，把该频域信号分解成有声频段（0-20khz）和超声频段（21khz-23khz）。

* 对于有声频段，我们使用MFCC声音特征提取算法，提取了24个维度的MFCC特征。由于在数据增强时使用了随机倍速调整，导致每个音频的MFCC特征长度是不同的，因此我们加入了padding把每个MFCC特征长度都加到了469的长度。
* 对于超声频段，我们使用STFT算法，将结果保存为图片。其中STFT窗口的大小设置为了采样频率的0.7倍，而overlap即重叠大小设置为了窗口大小的1/30。最终的多普勒频移谱图片大小为224*224像素的RGB三通道图片。

<img src="https://s2.loli.net/2022/03/25/xCTLwhIpsDaeJ49.jpg" style="zoom:50%;" /><img src="https://s2.loli.net/2022/03/25/JWpln6uCMRcF9hm.jpg" style="zoom: 90%;" />



（图1：MFCC结果可视化；图2：STFT频谱图）

#### 3.2 深度学习模型

##### 3.2.1 模型选择

在模型的选择方面我们尝试了多个模型，在序列模型方面我们尝试了Transformer、LSTM两种模型，在图片识别方面我们尝试了Res50、VGG，ViT三种模型。由于基于自注意力机制的Transformer模型比较依赖于数据集的大小，出于数据集的限制我们抛弃了Transformer和ViT这两个模型。在Res50和VGG方面，Res50更加的稳定、层数更深，因此表现的比VGG要好，最终我们选择了LSTM和Res50这两个模型。

##### 3.2.2 模型架构

首先，输入的是超声频段动作特征和有声频段声音特征两个模态的数据，分别是经过STFT和MFCC提取出来的特征矩阵。然后有声频段数据经过两层双向的LSTM模型输出一个长度为512的向量，超声频段经过Res50模型，从一个224\*224\*3维度映射到了一个长为1024大小的向量。然后二者拼起来成为一个1536的向量。最终放到一个全连接层中，映射到1024长度的特征向量编码，如果想要比较两个声音的相似度，直接将二者相减，再映射到一个节点上，通过sigmoid函数计算来自同一个人的概率。

<img src="https://s2.loli.net/2022/03/25/hrHZevqcGgowRbt.png" style="zoom:30%;" />

（模型结构）

##### 3.3.3 预训练任务

在模型训练中我们设置了两个训练任务，一个是针对于few shot任务的三元组训练，另一个是一对一的区别训练。

* 三元组训练。我们构建了拥有三个音频的数据，一个作为anchor，其余两个一个和anchor同类，另一个和anchor异类。我们的目标是获取特征向量编码之间的距离，使得同类更近异类更远。
* 一对一区别训练，让模型根据特征编码的相似度计算出是否来自同一个人的概率。

其中三元组训练依赖于模型计算出的特征向量编码，即最后的1024长度的向量。一对一区别训练则是依赖于最后sigmoid单元的输出，判断是否来自同一个人的可能性。

### 4.实验

#### 4.1 模型的训练

#### 4.2 系统的测试

### 5.结论

### Reference

* 
