# 验证新区块

比特币共识机制的第三步是网络中每个节点对每个新区块进行独立验证。随着新解决的区块在网络中传播，每个节点都会执行一系列测试来验证它。独立验证还确保只有遵循共识规则的区块才会被纳入区块链中，从而使其矿工获得奖励。违反规则的区块将被拒绝，不仅会使其矿工失去奖励，还会浪费寻找工作证明解决方案的努力，因此给这些矿工带来了创建区块的所有成本，但却没有给予他们任何奖励。

当一个节点收到一个新区块时，它将通过检查一个必须满足所有条件的长列表来验证该区块；否则，该区块将被拒绝。这些条件可以在比特币核心客户端的 CheckBlock 和 CheckBlockHeader 函数中看到，其中包括：

• 区块数据结构在语法上是有效的。&#x20;

• 区块头哈希小于目标值（实施工作证明）。

• 区块时间戳位于 MTP 和未来两小时之间（允许时间误差）。

• 区块权重在可接受范围内。&#x20;

• 第一个交易（仅第一个）是 coinbase 交易。&#x20;

• 区块中的所有交易都使用“交易独立验证”部分讨论的交易清单进行验证。

网络中每个节点对每个新区块的独立验证确保了矿工无法作弊。在前面的部分中，我们看到矿工可以编写一个交易，以奖励他们在区块内创建的新比特币并获取交易费。为什么矿工不会为自己编写一个价值一千个比特币的交易，而是正确的奖励？因为每个节点都根据相同的规则验证区块。无效的 coinbase 交易会使整个区块无效，导致该区块被拒绝，因此该交易永远不会成为区块链的一部分。矿工必须根据所有节点遵循的共享规则构建一个区块，并使用正确的 PoW 解决方案对其进行挖掘。为此，他们在挖掘过程中耗费了大量的电力，如果他们作弊，所有的电力和努力都将被浪费。这就是为什么独立验证是分散式共识的关键组成部分。