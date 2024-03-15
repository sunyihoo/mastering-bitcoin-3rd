# "信任之根"

信任之根是传统安全架构的一个概念，它是一个可信的核心，作为整个系统或应用安全的基础。安全架构围绕着信任之根构建，形成一系列同心圆，就像洋葱的层次一样，从中心向外扩展信任。每一层都基于更可信的内部层，使用访问控制、数字签名、加密和其他安全原语进行构建。随着软件系统变得越来越复杂，它们更容易包含漏洞，从而使它们容易受到安全威胁。因此，软件系统越复杂，保护它们就越困难。信任之根的概念确保了大部分信任被放置在系统中最不复杂、因此最不容易受攻击的部分，而更复杂的软件则围绕它进行分层。这种安全架构在不同的规模上重复出现，首先在单个系统的硬件中建立信任之根，然后通过操作系统将该信任之根扩展到更高级别的系统服务，最后通过许多服务器层层叠加在同心圆形的信任中。

比特币的安全架构则不同。在比特币中，共识系统创建了一个完全去中心化的受信任的区块链。一个正确验证的区块链使用创世区块作为信任之根，建立起到当前区块的信任链。比特币系统可以和应该使用区块链作为它们的信任之根。在设计一个由许多不同系统上的服务组成的复杂比特币应用时，您应该仔细检查安全架构，以确定信任被放置在哪里。最终，唯一明确可信的是一个完全验证过的区块链。如果您的应用明确或隐含地将信任置于除了区块链之外的任何东西，那应该是一个令人担忧的问题，因为这会引入漏洞。评估应用程序的安全架构的一个好方法是考虑每个单独的组件，并评估一个假设情景，即该组件完全被损害并受到恶意操作者控制。逐个考虑应用程序的每个组件，并评估如果该组件被损坏会对整体安全性产生什么影响。如果在组件被损坏时您的应用程序不再安全，那就表明您在这些组件中放置了信任。一个没有漏洞的比特币应用程序应该只对比特币共识机制的妥协具有漏洞，这意味着它的信任之根是建立在比特币安全架构的最强部分上。

大量比特币交易所被黑客攻击的例子强调了这一点，因为它们的安全架构和设计甚至在最简单的审查下也失败了。这些集中化的实施明确地将信任投资于比特币区块链之外的许多组件，例如热钱包、集中式数据库、易受攻击的加密密钥等。