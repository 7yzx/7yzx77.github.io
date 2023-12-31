---
layout: mypost
title: LightTrack 论文 + 代码
categories: [计算机视觉]
extMath: true
---


**1）摘要：背景-挑战- 方法- 贡献**

# 引言

## 背景 + 提出问题

现有的跟踪模型变得越来越庞大且计算成本昂贵，限制了在实际应用中的部署。

tracking models are becoming increasingly heavy and expensive.

## 目前的情况

两种解决这一问题的方法：模型压缩和紧凑模型设计。传统的压缩技术，如修剪和量化，可以减少模型的复杂性，但由于信息丢失不可避免地带来性能降级。与此同时，手工设计新的紧凑高效模型对工程而言昂贵且依赖于设计者专业知识和经验。

## 提出方法

即使用神经架构搜索自动设计轻量级模型，automating the design of lightweight models with neural architecture search (NAS)以便这些模型能够在资源有限的硬件平台上以高效的方式运行。

<strong>LightTrack</strong>.<strong>算法</strong>它将所有可能的架构编码到一个主干超网络和一个头部超网络中。

<strong>主干超网</strong>在 ImageNet 上进行预训练，然后使用跟踪数据进行微调，而头部超网络则直接在跟踪数据上进行训练。这两个超网络只需训练一次，然后每个候选架构直接从超网继承其权重。在经过训练的超网络上执行架构搜索，使用跟踪精度和模型复杂性作为监督指导。（看不懂）

另一方面，为了减少模型复杂性，我们设计了一个由轻量级构建块组成的搜索空间，例如深度可分离卷积和反向残差结构。这样的搜索空间允许一次性 NAS 算法寻找更紧凑的神经架构，在跟踪性能和计算成本之间取得平衡。

## 贡献

This work makes the following contributions.

• We present the first effort on automating the design of neural architectures for object tracking. We develop a new formulation of one-shot NAS and use it to find promising architectures for tracking.

•我们为目标跟踪设计了一个<strong>轻量级的搜索空间</strong>和一个<strong>专用的搜索流程</strong>。实验证明了所提方法的有效性。此外，搜索到的跟踪器达到了最先进的性能，并且可以部署在各种资源有限的平台上。

- 3）<strong>相关工作</strong>，  从研究问题【发展过程使用的一系列解决方法】和核心算法【本文提出的算法的发展历程、发展历史】两个方面展开， “针对问题提出方法存在不足”   以此循环展开 【最后一句话要说当前方法的缺点，从而引入我们的方法，并介绍我们方法的特点】

# 相关工作

## 目标检测发展

说了很多经典的相关滤波算法，还有 siamrpn 以及相关基于神经网络的，但是他们都存在一个问题，就是 additional computation workload and large memory footprint

## 提出了 Neural Architecture Search

本文要使用的算法

## 回顾 One-Shot NAS

在介绍作者提出的方法之前，文中先简要回顾了一次性神经架构搜索（One-Shot NAS）的方法，这是本文讨论的基本搜索算法。

一次性 NAS 将所有候选架构视为超网的不同子网络，并在具有<strong>共同组件的架构</strong>之间共享权重。具体而言，架构搜索空间 A 被编码成一个超网络 N (A, W )，其中 W 是超网络的权重。权重 W 在所有架构候选中共享，即在 <em>N</em> 中的子网络$\alpha \in A$中共享。最优架构$\alpha^*$的搜索被公式化为嵌套优化问题：

![](JPCubIADTocePoxxSyHc6lsLnRc.PNG)

其中<strong>约束函数</strong>是通过在<strong>训练数据</strong><strong>集</strong>上最小化<strong>损失函数</strong><strong> </strong>$L_{train}$ 来优化超网 N 的<strong>权重 W</strong>，而<strong>目标函数</strong>是通过基于学习到的超网络权重$W^*$ 在<strong>验证数据集</strong>上对子网的准确性 $Acc_{val}$进行排名来<strong>搜索架构</strong>。只需训练单一超网络 N 的权重，然后通过从一次性超网<strong>继承</strong><strong>已训练权重</strong>来<strong>评估</strong>子网络，而无需进行单独的训练。这大大加速了架构性能估计，因为不需要子网络训练，从而使方法只需几天的 GPU 时间。

为了减少内存占用，一次性方法通常从超网络 N 中对子网络进行采样以进行优化。为简单起见，本文采用<strong>单路径均匀采样策略</strong>，即每个批次只从超网络中随机选择一条路径进行训练。这种单路径一次性方法将超网络训练和架构优化解耦。由于不可能对所有架构 $\alpha \in A$ 进行性能评估，文中采用<strong>进化算法</strong>从一次性超网络中找到最有希望的子网。

# LightTrack

这段文本中提到了几个关键点:

1. 模型预训练和微调： 通常，目标跟踪器需要在图像分类任务上进行模型预训练以获得良好的初始化。与此同时，神经架构搜索（NAS）算法需要从目标任务中获取<strong>监督信号</strong>。搜索用于目标跟踪的架构需要同时考虑在 ImageNet 上的预训练和在跟踪数据上的微调。
2. 目标跟踪器的结构： 目标跟踪器通常由两个部分组成：用于特征提取的主干网络（backbone network）和用于目标定位的头部网络（head network）。在搜索新的架构时，NAS 算法需要将这两个部分作为整体考虑，以便发现适用于目标跟踪任务的结构。
3. 搜索空间的重要性： 搜索空间对于 NAS 算法至关重要，它定义了 NAS 方法原则上可能发现的神经架构。为了找到轻量级的架构，搜索空间需要包括紧凑和低延迟的构建块。

在这一部分，我们解决了前面提到的挑战，并提出了基于一次性神经架构搜索（NAS）的 LightTrack。首先，我们引入了一种专门针对目标跟踪任务的一次性 NAS 新的表达方式。然后，我们设计了一个轻量级的搜索空间，包括深度可分离卷积 [11] 和反向残差结构 [45, 23]，从而可以构建高效的跟踪架构。最后，我们介绍了 LightTrack 的流程，它能够为不同的部署场景搜索各种模型。

## Tracking via One-Shot NAS

> 这一部分是算法主体，主要讲了如何通过预训练找到共享权值，以及如何通过权值搜索在最优的主干网络和头部网络。

这一部分探讨了当前流行的目标跟踪器（例如[31, 14, 6]）对于其主干网络需要进行 ImageNet 预训练的情况。然而，对于架构搜索来说，不可能对所有的主干候选者在 ImageNet 上进行独立的预训练，因为计算成本非常高（ImageNet 预训练通常需要在 8 个 V100 GPU 上花费数天的时间才能为单个网络完成）。

受一次性 NAS 的启发，作者引入了权重共享策略，以避免每个候选者都从头开始进行预训练。具体而言，他们将主干架构的搜索空间编码到一个超网络 $N_b$ 中。这个主干超网络只需要在 ImageNet 上进行一次预训练，然后它的权重就被共享到不同的主干架构中，这些架构是一次性模型的子网络。

1. 背景预训练： 传统的目标跟踪器通常需要在 ImageNet 上对主干网络进行预训练，以获取良好的图像表示。但在架构搜索中，无法对每个主干网络候选者进行独立的 ImageNet 预训练，因为计算成本非常巨大。为解决这个问题，引入了一种权重共享策略，将主干网络架构的搜索空间编码到一个超网络 $N_b$ 中。该超网络只需在 ImageNet 上进行一次预训练，然后将其权重共享给不同的主干网络候选者，这些候选者是超网络的子网络。ImageNet 预训练通过优化分类损失函数$L_{pre-train}^{cls}$ 进行。

ImageNet 预训练通过优化分类损失函数 $L_{pre-train}^{cls}$ 进行，表示为：

其中

- $W^p_b$：这是主干超网络$N_b$的预训练权重，表示在 ImageNet 上进行<strong>预训练后得到的权重。</strong>
- $A_b
  $ 表示主干架构的搜索空间，而 $W_b$ 表示主干超网络 $N_b$ 的参数。
- $W_b$：这是主干超网络$N_b$的参数，表示用于搜索不同主干网络的权重。在公式中，$W_b$是一个待优化的变量。
- $L_{pre-train}^{cls}$：这是 ImageNet 上的分类损失函数，用于衡量主干超网络$N_b$在 ImageNet 数据集上的性能。通过最小化$L_{pre-train}^{cls}$，可以使$W_b$趋近于最优值，以便在搜索中更好地适应目标跟踪任务。

![](U0FybBDBcoqHfQxmzEHcbBC3nXb.png)

1. 主干和头部架构搜索： 为了在架构搜索中综合考虑主干和头部网络，构建了一个跟踪超网络 N，其中包括主干部分$N_b$ 和头部部分$N_h$，即$N = \{N_b,N_h\}$。首先，通过公式（2）对主干超网络$N_b$进行 ImageNet 预训练，并生成权重$W^p_b$。头部超网络$N_h$ 包含了空间$A_h$ 中的所有可能的定位网络。权重$W_b$ 在不同架构之间进行共享。通过在跟踪数据上进行联合搜索，公式（3）重新制定了一次性 NAS。在此过程中，通过对候选架构在跟踪数据验证集上的准确性进行排名，找到最佳的主干 $\alpha^*_b$和头部$\alpha^*_h$ 架构。

![](Kzl5beHypoeA4DxG4uFcLj1cnsH.png)

- 约束函数： 约束函数的任务是同时训练跟踪超网络 N 并优化权重 $W_b$ 和 W_h。这是通过解决一个联合优化问题来完成的，通过最小化训练损失函数（$L^{trk}_{train}$）对超网络的权重进行优化。
- 目标函数： 目标函数的任务是通过在跟踪数据验证集上评估候选架构的性能来找到最佳的主干网络架构 $\alpha^*_b$和头部网络架构 $\alpha^*_h$。这是通过排名候选架构在验证集上的准确性（$Acc_{val}^{trk}$）来完成的。
- 评估过程： 评估候选架构的性能时，只需要进行推理，而不需要额外的训练。这是因为架构的权重（$\alpha^*_b$和 $\alpha^*_h$）是从预训练权重 $W_b^*(\alpha_b)$和 $W_h^*(\alpha_h)$ 中继承而来的。
- 初始化： 在开始训练跟踪超网络之前，使用预训练的权重 $W_b^p$来初始化主干网络的权重 $W_b$。这有助于加速超网络的收敛并提高跟踪性能。
- 搜索过程： 在搜索空间中评估所有架构的性能是昂贵的，因此采用了进化算法来找到在验证集上表现最好的候选架构。这种方法在先前的工作中已经得到了应用，因为它是一种有效的方式来在大规模搜索空间中找到最佳架构。

![](YficbOWTFo9q6HxZ9Q5c45apnkf.png)

1. 约束和进化算法：
   在实际部署中，目标跟踪器通常需要满足附加的约束，如内存占用、模型 FLOPs、能耗等。主要考虑模型大小和 FLOPs 这两个关键指标。通过预设网络参数和 FLOPs 的预算，并强加约束条件，以确保跟踪器可以部署在特定资源受限的设备上。采用进化算法来搜索符合这些约束的最有前途的架构。该算法具有灵活性，能够直接控制突变和交叉过程，以生成适应于约束的候选者。整个搜索过程可以在训练好的超网络上进行多次，使用不同的约束条件，使一次性架构搜索在实践中成为了寻找适用于多样化部署场景的跟踪架构的实际且有效的方法。
   在实际部署中，目标跟踪器通常需要满足附加的约束条件，例如内存占用、模型 FLOPs（每秒浮点运算数）、能耗等。在这种方法中，主要考虑模型大小和 FLOPs 两个关键指标，这些指标用于评估跟踪器是否可以在特定资源受限的设备上部署。
   具体而言，通过设置网络参数（Params）和 FLOPs 的预算，并将以下约束条件施加在优化的架构上：
   这里，$\alpha^*_b$ 和 $\alpha^*_h$ 分别是主干网络和头部网络的最优架构，$Flops_{max}$ 和 $Params_{max}$ 是预先设定的 FLOPs 和参数的最大预算。
   进化算法在处理不同预算约束方面具有灵活性，因为可以直接控制变异和交叉过程，以生成符合约束条件的适当候选架构。此外，一旦训练了相同的超网络，可以多次进行搜索，使用不同的约束条件，从而使一次性范式在寻找适用于不同部署场景的跟踪架构方面变得实用且有效。

## Search Space

![](ND6wbpRAbomQopx0125cLnilnj3.png)

1. <strong>骨干空间（</strong><strong>Ab</strong><strong>）:</strong>

   - 骨干空间包括六个基本构建块，包括移动反向瓶颈（MBConv），其核大小为{3, 5, 7}，扩张率为{4, 6}。
   - 骨干候选模型通过堆叠这些基本块构建。
   - 空间中的所有候选模型都有四个阶段，总步幅为 16。
   - 每个阶段（除了前两个）包含多达四个用于搜索的块。
   - 骨干空间中有 14 个层，空间中包含大量可能的骨干架构（约 7.8 x 10^10）。
2. <strong>头部空间（</strong><strong>Ah</strong><strong>）:</strong>

   - 头部空间中的头部架构候选包括两个分支：一个用于分类，另一个用于回归。
   - 两个分支最多包含八个可搜索层。
   - 第一层是深度可分离卷积（DSConv），核大小为{3, 5}，通道数为{128, 192, 256}。
   - 接下来的七层按照第一层的通道设置，核选择为{3, 5}。
   - 为了实现头部架构的弹性深度，使用额外的跳跃连接。
   - 头部空间包含大量可能的搜索架构（约 3.9 x 10^8）。
     适应输出特征层:
   - 为了解决关于哪一层特征更适合目标跟踪的不确定性，搜索空间添加了一个新的维度。在超网络训练期间，从骨干超网络的最后八个块中随机选择一个末端层，并将其输出用作提取的特征。
   - 此策略允许在进化搜索期间评估不同可能的块，以确定哪一层更适合该任务。

搜索空间特征:

总的来说，这个搜索空间涵盖了各种架构，允许探索用于目标跟踪的轻量且高效的神经结构。

## Search Pipeline

流程，流水线

主要分成三个连续的阶段：预训练骨干超网络（pretraining backbone supernet）, 训练跟踪超网络 training tracking supernet, 使用进化算法进行搜索 and searching with evolutionary algorithm on the trained supernets.

![](LlDobpHw3o8HehxKjPMcrocxn9N.png)

1. <strong>第一阶段：预训练骨干超网络</strong>
   - 骨干超网络（$N_b$）编码了搜索空间$A_b$中所有可能的骨干网络。
   - 预训练的目标是通过在 ImageNet 上优化交叉熵损失，这是由方程（2）定义的。
   - 使用均匀路径采样，每个批次只随机选择一条路径进行前向和后向传播，冻结其他路径的权重。

换句话说，每个批次中只有一条随机路径用于前向和后向传播，而其他路径的权重被冻结。这有助于确保在预训练期间不同子网络的权重独立。

1. <strong>第二阶段：训练跟踪超网络</strong>
   在这个阶段，训练了跟踪超网络 N，其结构在图 2（中部）中可视化。本质上，它是 Siamese 追踪器的一种变体。
   具体而言，它以一对跟踪图像作为输入，包括一个示例图像和一个搜索图像。示例图像代表感兴趣的对象，而搜索图像代表随后视频帧中的搜索区域。两个输入都经过预训练的骨干网络进行特征提取。生成的两个特征图进行互相关操作以生成相关体积。头网络包含一个用于目标分类的分支和一个用于目标定位的回归分支。头超网络的体系结构可以在表 1 中找到。训练还采用了单路径均匀采样方案，但涉及跟踪头和度量标准。在每次迭代中，优化器会更新从骨干和头超网络中随机采样的一条路径。损失函数 Ltrk train 在方程（3）中包括用于前景-背景分类的常用二元交叉熵损失和用于目标边界框回归的 IoU 损失。

   - 输入: 一对跟踪图像，包括示例图像和搜索图像。
   - 处理: 两个输入经过预训练的骨干网络进行特征提取，生成的特征图进行互相关操作。
   - 头网络: 包含用于目标分类和目标定位的分支。
   - 训练方案: 采用单路径均匀采样，每次迭代中更新一条随机路径。
   - 损失函数: 二元交叉熵损失用于前景-背景分类，IoU 损失用于目标边界框回归。
     这个阶段的目标是训练一个具有良好性能的跟踪超网络，以便在第三阶段中进行进化搜索。
2. 第三阶段：使用进化算法进行搜索
   在这个阶段，对训练过的超网络执行进化搜索。在进化控制器的指导下，从超网络中挑选路径并进行评估。首先，随机初始化一组架构。选择前 k 个架构作为父架构，用于生成子网络。通过突变和交叉生成下一代网络。对于交叉，随机选择两个候选网络进行交叉以生成一个新的网络。对于突变，随机选择一个候选网络，以 0.1 的概率对其每个选择块进行突变，以生成一个新的候选网络。交叉和突变被重复执行，以生成足够数量的新候选网络，满足方程（4）中给定的架构约束。关于批标准化[26]的一个必要细节是，在搜索期间，子网络从超网络中以随机方式进行采样。问题在于一条路径上的批次统计应独立于其他路径[20, 9]。因此，在推断之前，我们需要为每条单独的路径（子网）重新计算批次统计。我们从跟踪训练集中随机抽取一个子集，以重新计算将要评估的单一路径的批次统计。这是非常快速的，只需要几秒钟，因为没有涉及反向传播。

# 代码笔记

结构：

dataset 是数据集

experiment 有一个 yaml 文件

lib 是很多工具文件，比如读取 groudtruth 以及跟踪器实现

snapshot 预训练权重文件

tracking 包含了运行代码

## test_lighttrack.py

主要功能是测试网络怎么样，fps，还有 lost 的多少

具体：

传入参数-------->

建立跟踪器（Lighttrack）---------->

```python
siam_tracker = Lighttrack(siam_info, even=args.even)
```

建立 siamese  看不懂。。。。。。

```python
networksiam_net = models.__dict__[args.arch](args.path_name, stride=siam_info.stride)
```

（为啥）-------------

init Siamese---------

```python
siam_net = load_pretrain(siam_net, args.resume)
    siam_net.eval()
    siam_net = siam_net.cuda()
```

加载视频数据集------------------

开始追踪（track 函数）  

`track(siam_tracker, siam_net, dataset[args.video], args)`

# Ml-dl 的一些英文单词

baseline：简单说就说自己的方法和别人的原来的对比的方法

pipeline：是自己的方法的流水线，过程

benchmark：对比别人的办法

crop：      裁剪，从图片中裁剪
