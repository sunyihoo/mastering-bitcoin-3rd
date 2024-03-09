# 获取正确的输入

Alice 的钱包应用程序首先需要找到可以支付给 Bob 的金额的输入。大多数钱包跟踪钱包中地址所有可用的输出。因此，Alice 的钱包会包含来自 Joe 交易的交易输出的副本，该交易是用现金交换的（参见“获取您的第一个比特币”）。在全节点上运行的比特币钱包应用程序实际上包含了每个已确认交易的未花费输出，称为 UTXO（未花费的交易输出）。然而，由于全节点使用的资源更多，许多用户钱包运行轻量级客户端，仅跟踪用户自己的 UTXO。

在这种情况下，这个单一的 UTXO 足以支付播客。如果不是这样，Alice 的钱包应用程序可能需要合并几个较小的 UTXO，就像从钱包中选取硬币一样，直到它找到足够支付播客的金额。在这两种情况下，可能需要得到一些找零，我们将在下一节中看到，当钱包应用程序创建交易输出（支付）时。