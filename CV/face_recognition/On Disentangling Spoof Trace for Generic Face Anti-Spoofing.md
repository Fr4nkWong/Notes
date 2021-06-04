# Interpretation

​		论文中提出的方法能侦测到欺骗人脸、分离出欺骗痕迹、重建欺骗人脸的live版本. 之前的研究表明，面对反欺骗的关键在于其微妙的图像模式，即所谓的“欺骗痕迹”，比如颜色失真、3D面具边缘、莫列波纹等. 设计一个通用的反欺骗模型去估计欺骗痕迹，不仅能提高欺骗侦查的泛化能力，还能提高模型决策的可解释性. 这是一个有挑战性的任务，因为欺骗类型的多样性和欺骗痕迹中GT(**ground truth**)的缺乏. 论文中设计了一个新的对抗性学习框架，从输入的人脸中分离出欺骗痕迹，作为多尺度中分层级的模式组合. 通过欺骗痕迹的分离，作者揭露了原始欺骗人脸的live版，在适当的几何矫正后进一步合成了现实的新的欺骗人脸. 作者的方法在可见的和看不见的欺骗场景上都展示了优越的欺骗检测性能，同时提供了对欺骗痕迹的可视化的、令人信服的估计.

​		基于深度学习的方法有2个限制：1）先前大多数工作涉及的欺骗类型有限，不是打印\重放攻击就是3D面具攻击，然而现实世界反欺骗系统或许会遇到各种各样的欺骗类型，包括打印、重放、3D面具和面部化妆. 2）许多方法将人脸反欺骗仅仅表述为一个分类/回归问题.

​		论文认为所有现有欺骗类型的人脸欺骗检测是**generatic face-anti spoofing**，并将区分欺骗人脸和其对应live版本的模式称为**spoof trace**(欺骗痕迹). 论文旨在使通用人脸反欺骗模型具备明确地从输入人脸中提取spoof trace的能力，这个过程被称为**spoof trace disentanglement**(欺骗痕迹分离). 解决该问题有以下好处：1）FAS二元分类会获取任何会帮助分类的线索，包括与欺骗无关的线索(eg:光照)，因此会阻碍泛化性. 相比之下，spoof trace disentanglement明确解决了欺骗中最基本的线索，可以基于其分类并见证更好的泛化性. 2）随着追求AI的可解释性是趋势，FAS模型需要生成支持二元决策的欺骗模式，spoof trace可以给模型决策提供一个很好的视觉解释. 基于这些痕迹，欺骗攻击的某些属性(eg:严重性、方法)可能会被揭露. 3）spoof trace是生成逼真欺骗样本的很好的来源. 高质量的合成可以解决少数欺骗类型的训练数据有限的问题(eg:特殊的3D面具、化妆).		

​		以下是本论文工作的主要贡献：1）首次研究了用于通用FAS的spoof trace. 2）作者提出了一种新的模型，将spoof trace分离成一个层次表示. 3）使用spoof trace去生成新数据、强化训练. 4）达到了SOTA反欺骗性能度量，并提供了令人信服的可视化.

**Spoof Trace Disentanglement Network(STDN)**

​		只给定live或spoof二元标签，STDN采取了全面的GAN训练策略. 生成器(Disentanglement Generator)获取输入，检测欺骗人脸，将spoof trace分离为多个元素的组合**{s,b,C,T}**. 通过spoof trace，spoof图通过运算可得到重构的live图(**Reconstructed Live**). 新的live图可与spoof trace通过运算得到合成的spoof图(**Synthesized Spoof**). 为了在合成spoof图时纠正几何上的差异，论文提出了**3D warping layer**用于将spoof traces变形为目标脸. 作者部署了多尺度的鉴别器(Muti-scale Discriminator)来提高Reconstructed Live和Synthesized Spoof的保真度. 此外，Synthesized Spoof进一步被用于以有监督的方式训练生成器，这归功于分离的欺骗痕迹作为合成样本的基本事实. 

​		该论文不仅要获取正确的预测(live/spoof)，还要获取spoof trace的可靠估计. <u>在训练样本不存在标记出spoof trace的情况下，关键点是找到从输入转移到live域时的最小变化.</u>  L-2正则化是spoof trace的首选，因为没有配对的解决方案且我们希望spoof trace是有界的. 基于[24]\[37]，spoof trace可根据比例划分多个元素：global trace、low-level trace和high-level trace. Global trace，如颜色平衡偏差和范围偏差，可以有效地用单个值来建模. 这里的颜色偏差只指欺骗媒介和捕捉相机之间的交互所产生的偏差，预测模型应该忽略那些与欺骗无关的颜色变化. Low-level trace，由平滑的内容模式组成，如化妆、镜面亮点. High-level trace，包括清晰的图案和高频纹理，如掩模边缘和Moir图案. 

​		分离形势的表示能极大提高分离质量，这种解纠缠表示**spoof trace elements{s, b, C, T}**可以大大提高分离的质量，并抑制其从粗到细的过程中不需要的伪影. 

**Disentanglement Generator**



**Reconstruction & Synthesis**

​		该论文采用了两种方式来从spoof traces中获益，分别是：Reconstruction和Synthesis. Reconstruction从输入face中获得其对应的live版face，即reconstructed live. Synthesis将之前分离出的spoof traces应用到新的的face上，从而获得一个新的spoof face，即synthesized spoof. 

​		然而Synthesis的过程中，之前从spoof face中分离出的spoof traces和新的live face结合时很可能会出现明显的不合适，因此需要几何校正. 论文中提出3D wraping layer便是用于解决这个问题，它将之前的spoof traces加上**dense offset**得到适应新face的**warped traces**. 为了得到dense offset，作者先拟合3DMM模型去提取spoof face和live face的2D位置，然后得出**sparse offset**，采用 *Delaunay triangulation interpolation*根据sparse offset得到dense offset. 

​		

**Multiscale Discriminator**



# Related works

**Face Anti-Spoofing**

​		FAS的发展大致可分为三个阶段：1）在早期，研究人员利用人类自发的运动，如眨眼和头部运动，来检测简单的打印照片或静态回放攻击. 然而，当面对反击攻击时，如带有眼睛区域切割的打印脸和重放面部视频时，这些方法将会失败. 2）后来，研究人员更多地关注现live和spoof之间的纹理差异，这是spoof媒体所固有的. 研究人员主要从脸上提取手工制作的特征，如LBP、HoG、SIFT、SURF，训练一个分类器去辨别live和spoof，如SVM、LDA. 3）最近，面对反欺骗解决方案配备了深度学习技术，训练一个深度神经网络来学习一个live和spoof之间的二元分类器，并证明了比传统方法的显著改进. 额外的监督，如人脸深度图、rPPG信号，被提出来帮助网络去学习更具泛化性的特征. 随着最新的方法在几个基准上达到饱和性能，研究者开始探索更具挑战性的案例，如少镜头/零镜头FAS、领域适应的FAS.

​		在本论文中，作者旨在解决一个有趣但很有挑战性的问题：<u>从输入面中分离和可视化欺骗痕迹</u>. 有不少相关工作采用GAN以寻求估计不同的痕迹，然而他们将痕迹表示为低强度噪声，仅限于打印和重放攻击，不能提供令人信服的可视化结果. 相比之下，作者从范围广泛的欺骗攻击中探索spoof trace，用新的disentanglement来可视化，并在具有挑战性的案例下评估了提出的方法(eg: zero-shot FAS). 

**Disentanglement learning**

​		Disentanglement learning常用来更好地表示复杂数据和特征. DR-GAN[46]\[47]将人脸分解为身份向量和姿态向量，用于姿态不变的人脸识别和视图合成. 同样，在步态识别中，[53]从输入步态视频中分离出外观、规范和姿势特征的表示. 3D重建工作[28]也将三维人脸的表示分解为身份、表达式、姿势、反照率和照明. 

# References

Moir Pattern: https://blog.csdn.net/vily_lei/article/details/86698473

ground truth: https://www.zhihu.com/question/22464082/answers/updated

upsampling&downsampling: https://blog.csdn.net/majinlei121/article/details/46742339