# Face Anti-spoofing 

**Link**

2019CVPR_人脸活体检测：https://zhuanlan.zhihu.com/p/69733383

2019FAS挑战赛：https://zhuanlan.zhihu.com/p/69542283

2020FAS综述：https://zhuanlan.zhihu.com/p/43480539

**Paper**

- Exploiting temporal and depth information for multi-frame face anti-spoofifing
  - Zezheng Wang, Chenxu Zhao*, Yunxiao Qin, Qiusheng Zhou, Guojun Qi,  Jun Wan, Zhen Lei
  - JD Finance, Huawei Cloud, Chinese Academy of Sciences
  - https://zhuanlan.zhihu.com/p/114313640

```
# abstract
- 先前在深度监督学习上的工作已经证明了用于FAS任务的效果很好，然而它们只考虑了单帧中深度作为辅助监督. 与这些方法不同，作者开发出一种新的方法去从多个RGB帧中估计深度信息，并提出了一种深度监督体系结构，能有效地对时空信息进行编码，用于表示攻击检测.
- 深度监督体系结构包括2个模块: OFFB和ConvGRU，它们用来提取短期和长期动作去分辨真、假人脸.
# introduction
- 为了估计FAS任务中的面部深度，作者提出了对比深度损失(CDLoss, Contrastive Depth Loss)来学习面部点的地形. 
- 论文主要贡献：1) 分析FAS中的时间深度，寻找FAS中运动和深度中的有用的东西. 2）提出了一种带有OFFB和ConvGRU模块的深度监督体系结构，从单目帧序列的时间信息中揭露面部深度和它们不同的运动模式. 3) 设计出Contrasive Depth Loss去学习用于深度监督面部欺骗的面部点地形. 4) 在广泛使用的人脸反欺骗基准测试上，作者展示了比最先进的方法优越的性能.

# approach
## depth supervised single-frame architecture
- depth generation
	- 用面部深度图去训练单帧网络，从2d平面图中揭示面部定位和脸的3d形状，在真\假人脸的识别中提取有用的信息. 为了区分真、假脸，我们将真脸的深度图规范化在一个[0,1]范围内，同时将假脸的深度图设置为0. 作者采用密集面对准方法PRNet来估计真脸的三维形状，PRNet可用于将完整人脸的3d形状投射到UV空间. 通过该方法可得到了一组表示面部关键点的n个三维坐标的顶点V n×3. 由于这些坐标在映射到二维平面图像时是稀疏的，因此作者插值它们以获得密集的面坐标. 通过将插值坐标映射和归一化到二维平面图像，生成的面部深度图可以表示为D R32x32
- network structure
	- CDLoss旨在学习每个像素的地形，这限制了从像素点到其相邻点的对比度.
```

- *Vec2Face: Unveil Human Faces From Their Blackbox Features in Face Recognition*	CVPR2020
  - 

```
- 
- This paper presents a generative structure with bijective metric learning, DiBiGAN, for synthesizing faces of an identity given that person’s features.
- A distillation process is introduced to maximize the information exploited from the blackbox face recognition engine.
- A Feature-Conditional Generator Structure with Exponential Weighting Strategy is presented for a more robust generator that can synthesize realistic faces with ID preservation.
```

- Searching Central Difference Convolutional Networks for Face Anti-Spoofing	CVPR2020
  - Zitong Yu, Chenxu Zhao, Zezheng Wang, Yunxiao Qin, Zhuo Su, Xiaobai Li, Feng Zhou, Guoying Zhao*
  - 奥卢大学，明略科技，Aibee，西工大

```
# Abstract
- 当前大多数前沿的人脸反欺骗(FAS)方法的特点：
	1. 依赖于堆叠卷积和专家级设计的网络，这在描述详细的细粒度信息方面很弱，并且在环境变化时很容易失效. eg:不同的光照
	2. 更喜欢使用长序列作为输入来提取动态特征，使得它们很难部署到需要快速响应的场景中.
- 该论文提出了一个新的基于中心差分卷积(CDC)的帧级FAS方法，它能够通过聚合强度和梯度信息来捕获内在的详细模式. 用CDC构建的网络被称为中心差分卷积网络(CDCN)，该网络能够提供比普通卷积网络更强大的建模能力.
- 在一个专家设计的CDC搜索空间上，神经结构搜索(NAS)发现了一个更强大的网络结构(CDCN++)，该网络可以与多尺度注意融合模块(MAFM)相结合以进一步提高性能.
- 作者在六个基准数据集上的进行了综合实验：提出的方法不仅在数据集内测试(特别是OULU-NPU数据集协议1中的0.2%ACER)中取得了更好的性能，它还很好地推广了交叉数据集测试(特别是6.5%HTER从CASIA MFSD到重放攻击数据集)
- ACER和HTER是CV指标：https://blog.csdn.net/qq_16468675/article/details/107524162

# Introduction
- 人脸识别对于表示攻击(PA)的脆弱性限制了它的可靠部署. FAS方法用于侦察表示攻击(PAD).
- 表示攻击的经典例子：打印、视频回放、3D面具.
- 一方面，经典的手工描述(eg:局部二元模式LBP)利用领域间的局部关系作为鉴别特征，这能很好地描述真、假的人脸之间的细节不变信息(颜色纹理、莫列波纹、噪声伪影). 另一方面，由于具有非线性激活的叠加卷积运算，CNN具有很强的表示能力去区分真图像和表示攻击. 基于CNN的方法专注于更深的语义特征，这使得很难在真、假人脸之间描述详细的细粒度信息，而且很容易在环境变化(不同的光照)时失效. 如何将局部描述与卷积运算相结合以实现健壮的特征表示是一个值得探讨的问题！
- 最近基于深度学习的FAS方法经常建立在基于图像分类的骨架上，比如VGG、ResNet、DenseNet. 神经网络通常由二元交叉熵损失来监督，它很容易学习屏幕边框等任意模式，而不是欺骗模式的本质！为了解决这一问题，几个深度监督FAS方法(利用伪深度地图标签作为辅助监督信号)已被开发出来，但这些由专家精心设计的网络或许不是FAS任务最好的选择. 因此，应考虑在辅助深度监督下自动发现最适合FAS任务的网络！
- 现有的FAS方法大多需要多帧作为输入来提取PAD的动态时空特征(eg:运动、rPPG)，但是长视频序列或许对于需要快速决策的特殊部署场景不合适，因此，从可用性角度看，帧级PAD是有利的，尽管与视频级方法相比性能较差. 故设计高性能的帧级方法对于现实FAS应用是至关重要的.
- 基于上述讨论，作者提出了擅长描述细粒度不变信息的CDC. 相比普通卷积，CDC在不同环境中更有可能提取出内在的欺骗模式. 此外，在一个专门设计的CDC搜索空间上，利用神经结构搜索(NAS)发现了用于深度监督人脸反欺骗任务的优秀帧级网络.
- 论文贡献：
	- 提出适合FAS任务的CDC，因为它对不同环境下不变的细粒度特征具有显著表示能力. 在不引入任何额外参数的情况下，CDC可以取代现有神经网络中的普通卷积和即插即用，形成具有更强大建模能力的CDCN.
	- 提出CDCN++，这是CDCN的扩展版，它由搜索骨干网络和MAFM组成，有效地聚合了多级CDC特征.
	- 这是目前为止，第一个为FAS任务搜索神经结构的方法. 与以前基于分类任务受softmax损失监督的NAS不同，它在专家设计的CDC搜索空间里搜索用于深度监督FAS任务最适合的帧级网络.
	- 提出的方法通过内部测试和跨数据集测试，在所有六个基准数据集上实现了最先进的性能.
	
# Related Work
- 人脸反欺骗 FAS
	- 传统的FAS方法通常会从人脸图像中提取手工制作的特征来捕捉欺骗模式.
	- 几个经典的局部描述符(LBP、SIFT、SURF、HOG、DoG)用于提取帧级特征，而视频级方法通常捕获动态线索，如动态纹理、微运动和眼睛闪烁. 
	- 最近一些基于深度学习的方法被踢出用于帧级和视频级人脸的防欺骗. 对于帧级方法，预先训练的深度CNN模型被微调以提取在二进制分类设置中的特征. 相比之下，辅助深度监督FAS方法被引入去有效地学习更多详细的信息.另一方面，一些视频级CNN方法被提出以探索动态时空或用于PAD的rPPG特征. 尽管达到了最先进的性能，视频级深度学习的方法需要长序列作为输入. 此外，与传统的描述符相比，CNN很容易过拟合且在看不见的场景上很难一概而论.
- 卷积算子
	- 卷积算子是深度学习框架中提取基本视觉特征的常用算法. 最近，普通卷积算子的扩展被提出. 其中一个方向里，经典的局部描述符(eg: LBP和Gabor滤波器)被考虑到卷积设计中，代表作包括局部二元卷积和Gabor卷积，它们分别被提出以节省计算成本和提高对空间变化的抵抗力. 另一个方向是修改要进行聚合的空间范围，两个相关工作是拨号卷积和变形卷积. 然而这些卷积算子对于FAS任务或许不合适，因为不变细粒度特征的表示容量有限.
- 神经结构搜索 NAS
	- 在作者致力于寻找一个高性能的深度监督模型而不是一个用于人脸反欺骗任务的二元分类模型的同时，受到了最近NAS研究的启发. 
	- 现有NAS方法有三大类：1.基于强化学习；2.基于进化算法；3.基于梯度
	- 大多数NAS方法在一个小代理任务上搜索网络，并将找到的结构转移到另一个大目标任务上. 从CV应用的视角来看，NAS已经被开发用于人脸识别、行为识别、person ReID、目标检测、分割任务，目前还没有提出过基于NAS的方法来执行人脸反欺骗任务.
- 为了克服上述缺点并填补空白，作者在一个专门设计的搜索空间上搜索带有新提出的卷积算子的帧级CNN进行深度监督FAS任务.

# Methodology
- CDC
	- 由于卷积操作在信道维数上保持不变，本节为了简单起见将卷积在2D中描述，而扩展到3D是简单的. 
	- 2D卷积的主要步骤：1)在输入特征图x上取样局部感受野区域R. 2)通过加权求和来聚合采样值.
	- 对于FAS任务，强度级语义信息和梯度级详细信息对于区分真、假人脸都是至关重要的，这表明将宁香草卷积与中心差分卷积结合可能是一种可行的方式，以提供更稳健的建模能力.
	- CDC推广：VanillaConv + CDC
	- CDC与vanillaConv、LBConv、GaborConv的关系. 
- CDCN for FAS
	- 深度监督的人脸反欺骗方法利用了基于3D形状的欺骗人脸与真实人脸的区分，为FAS模型提供像素级的细节信息，以捕获欺骗线索.
	- 为了提取更细粒度和鲁棒性的特征以估计人脸深度图，作者提出了CDCN.
	- 关于theta的消融研究
	- 关于损失函数，MSELoss像素级监督. 此外，为满足FAS任务中细粒度监督的需求，CDLLoss被认为有助于神经网络学习更详细的特征. Loss=MSELoss+CDLLoss
- CDCN++: the searched networks with attention mechanism
	- 受经典视觉对象理解模型的启发，作者提出了CDCN扩展版，即CDCN++，它由一个基于NAS的backbone和具有选择性注意能力的多尺度注意融合模块(MAFM)组成.
	- 寻找用于FAS任务的backbone. 论文中的搜索算法基于2种基于梯度的NAS算法，目的是在低、中、高三个层次搜索cell以形成一个用于FAS任务的网络骨架.
	- backbone由三个具有最大池层的cell堆叠组成，stem层和head层采用3×3核的CDC，θ=0.7
	- cell中的有向边表示2个节点间信息流关系，它由架构参数加权的候选操作组成.
	- 操作空间由8个候选信道组成，其中cdc_2_r表示使用2个堆叠的CDC，先以r的比率增加信道数，然后再减小到原始信道大小. 
	- varied cells
	- 设置是f(o)最大的o; 对于中间结点，在指向自身的边中选择f(o)值最大的边; 对于输出结点，在
	- 虽然简单地融合低中高级别功能可以提高搜索的CDC体系结构的性能，但它仍很难关注到重要区域，这不利于学习更多具有鉴别性的特征. 受到人类视觉系统的注意力机制的启发，不同水平的神经元在其感受野中可能有不同注意力的刺激. 故作者提出了MAFM，它能通过空间注意力提炼和融合低-中-高层次的CDC特征.
	- MAFM的机制：来自不同层次的特征通过空间注意以及与感受野相关的普通卷积内核大小来提炼，最后再拼接在一起. CDC在这里没被选择是因为它的全球语义认知能力有限，这在空间注意力中至关重要. 此处涉及到相应的消融研究.
# Experiments
- learning rate, weight decay, batch size, epoches, adam optimizer, alpha, beta
- 数据库
- 性能度量
  - Attack Presentation Classification Error Rate (APCER)
  - Bona Fide Presentation Classification Error Rate (BPCER)
  - ACER
  - Half Total Error Rate(HTER) 用于交叉测试
  - Area Under Curve(AUC) 用于数据集内交叉测试
  - Equal Error Rate(EER) 用于SiW-M的交叉测试
- ablation study
	- CDCN中theta的影响
	- NAS配置的影响: varied cells, node attention
- intra testing
	- oulu-npu
	- siw
- inter testing 内部测试
	- 交叉测试
	- 跨数据集测试
	
# related work
NAS: 搜索空间、策略、性能度量.
# Conclusion & Future work

Q&A
- 为什么只进行Y通道的超分？
```



- Cross-Domain Face Presentation Attack Detection via Multi-Domain Disentangled Representation Learning	CVPR2020
  - Guoqing Wang, Hu Han, Shiguang Shan, Xilin Chen
  - 中科院；国科大；鹏城实验室

```

```



- Deep Spatial Gradient and Temporal Depth Learning for Face Anti-Spoofing	CVPR2020
  - Zitong Yu, Chenxu Zhao, Zezheng Wang, Yunxiao Qin, Zhuo Su, Xiaobai Li, Feng Zhou, Guoying Zhao
  - Aibee，奥卢大学，明略科技，中科院，西工大，京东

```

```



- Single-Side Domain Generalization for Face Anti-Spoofing
  - Yunpei Jia, Jie Zhang, Shiguang Shan, Xilin Chen
  - 中科院；国科大

```

```



- Face X-Ray for More General Face Forgery Detection
  - Lingzhi Li, Jianmin Bao, Ting Zhang, Hao Yang, Dong Chen, Fang Wen, Baining Guo
  - 北大；微软亚洲研究院  

```

```



- DeeperForensics-1.0: A Large-Scale Dataset for Real-World Face Forgery Detection
  - Liming Jiang, Ren Li, Wayne Wu, Chen Qian, Chen Change Loy
  - 南洋理工大学，商汤科技

```

```



- On the Detection of Digital Face Manipulation
  - Hao Dang, Feng Liu, Joel Stehouwer, Xiaoming Liu, Anil K. Jain
  - 密歇根州立大学

```

```



- Global Texture Enhancement for Fake Face Detection in the Wild
  - Zhengzhe Liu, Xiaojuan Qi, Philip H.S. Torr
  - 牛津大学；香港大学
