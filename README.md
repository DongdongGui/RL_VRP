# RL_VRP
VRP的目标是找到总成本最小的一组路径，每条路径中车辆从指定的仓库出发并最终回到 仓库，路径上的总需求不能超过车辆的承载能力。求解VRP的算法可分为精确算法和启发式算法。精确算法提供了最优的保证解，但由于计算复杂度高，无法处理大规模算例，而启发式算法往往速度快，但由于没有精确理论保证往往只能得到次优解。考虑到最优性和计算代价之间的权衡，启发式算法可以在大规模算例可接受的运行时间内找到次优解。然而，设计性能良好的启发式算法并不容易。
设计启发式算法是一个繁琐的过程。我们可以使用深度强化学习的方法来设计完全端到端的神经网络，在不需要人工干预的情况下自动地学习到隐含的启发式信息，尽可能地达到与启发式算法相近的效果。
对于本文求解的最经典的带容量限制的车辆路径规划问题(CVRP)，一辆有特定容量限制的车辆负责从仓库节点出发，需要将货物运送到多个客户节点，当车辆容量不足以满足任何客户点的需求时，必须返回仓库将货物装满。该问题的解决方案可以看作一组路径序列，每个路径序列的起点和终点都在车站，中间经过的都是客户节点。
VRP可以看作是一个连续的决策问题，可将编码器-解码器(Encoder-Decoder)架构作为解决这类问题的有效框架。以神经机器翻译(NMT)为例，编码器从源语言文本中提取句法结构和语义信息，然后解码器根据编码器给出的特征构造目标语言文本。由于求解VRP的输入输出均为特定内容的序列，所以编码器-解码器架构也可以用来求解VRP。首先利用编码器提取输入实例的特征，然后经过一系列的处理，最后用解码器迭代地构造解，在每个解序列的构建过程中，解码器预测选择每个节点的概率分布，然后选择一个节点将其放到当前已生成序列的末尾。因此，对每个输入实例X和模型的所有参数θ 该方法生成的解序列概率可由链式法则分解为如下:
本文的Encoder-Decoder框架借鉴了论文"Attention is all you need，31st Conference on Neural Information Processing Systems (NIPS 2017), Long Beach, CA, USA" 中提出的transformer模型，引入一个上下文向量来表示解码上下文，使用具有带确定性贪心基准值(greedy rollout baseline)的深度强化学习算法对模型进行训练。该模型将一个算例视为一个图，并提取节点特征来表示这样一个复杂的图结构，该结构捕获节点在其图邻居上下文中的属性，并根据这些节点特性逐步构造解。
