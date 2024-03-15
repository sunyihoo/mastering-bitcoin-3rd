# 施乃尔(Schnorr)签名

1989年，克劳斯·施乃尔发表了一篇论文，描述了他的同名签名算法。该算法并不特定于椭圆曲线密码学（ECC），尽管它如今可能与ECC联系最为密切，ECC是比特币和许多其他应用程序使用的密码学。施乃尔签名具有许多良好的特性：

\
可证明安全性

对施乃尔签名安全性的数学证明仅依赖于解决离散对数问题（DLP）的难度，特别是对于比特币所使用的椭圆曲线（EC），以及哈希函数（比特币中使用的SHA256函数）产生不可预测的值的能力，称为随机预言模型（ROM）。其他签名算法可能具有额外的依赖项，或者需要更大的公钥或签名才能获得与ECC-Schnorr相同的安全性（当威胁定义为经典计算机时；其他算法可能提供更有效的安全性，针对量子计算机）。

线性性

施乃尔签名具有数学家称为线性性的属性，该属性适用于具有两个特定属性的函数。第一个属性是将两个或多个变量相加，然后对该总和运行函数将产生与分别对每个变量运行函数然后将结果相加相同的值，例如，f(x + y + z) == f(x) + f(y) + f(z)；这种属性称为可加性。第二个属性是将一个变量乘以一个数，然后对该乘积运行函数将产生与对变量运行函数然后乘以相同量的值相同的值，例如，f(a × x) == a × f(x)；这种属性称为一次齐次性。

在密码学操作中，一些函数可能是私有的（例如涉及私钥或秘密随机数的函数），因此能够在函数内外执行操作获得相同结果，使得多个方可协调合作而无需分享其秘密。我们将在“基于施乃尔的无脚本多重签名”（第193页）和“基于施乃尔的无脚本阈值签名”（第195页）中看到线性性的具体好处。

批量验证

当以某种方式使用（比特币即如此）时，施乃尔的线性性的一个后果是，相对而言，可以比独立验证每个签名所需的时间更简单地同时验证多个施乃尔签名。在一个批次中验证的签名越多，加速度越大。对于一个块中的典型签名数量，可以在大约一半的时间内批量验证它们，而这将花费验证每个签名的时间。

在本章的后面，我们将精确描述施乃尔签名算法，就像它在比特币中使用的那样，但我们将从一个简化版本开始，并逐步逼近实际的协议。

Alice首先选择一个大的随机数（x），我们称之为她的私钥。她还知道比特币椭圆曲线上的一个公共点称为生成器（G）（参见“公钥”）。Alice使用椭圆曲线乘法将G乘以她的私钥x，其中x被称为标量，因为它对G进行了缩放。结果是xG，我们称之为Alice的公钥。Alice将她的公钥提供给Bob。尽管Bob也知道G，但离散对数问题阻止Bob能够通过G将xG除以G来推导出Alice的私钥。

在以后的某个时间，Bob希望Alice通过证明她知道用于前面接收的公钥（xG）的标量x来证明自己的身份。Alice不能直接给Bob x，因为那样会使他能够向其他人证明自己是她，因此她需要在不向Bob透露x的情况下证明她对x的知识，这称为零知识证明。为此，我们开始施乃尔身份验证过程：

1. Alice选择另一个大的随机数（k），我们称之为私密nonce。她再次将其用作标量，将其与G相乘以产生kG，我们称之为公共nonce。她将公共nonce提供给Bob。
2. Bob选择自己的一个大随机数e，我们称之为挑战标量。我们称之为“挑战”，因为它用于挑战Alice证明她知道用于前面向Bob提供的公钥（xG）的私钥x；我们称之为“标量”，因为它稍后将用于乘以一个椭圆曲线点。
3. Alice现在拥有数（标量）x、k和e。她将它们组合在一起，使用公式s = k + ex来生成最终的标量s。她将s提供给Bob。
4. Bob现在知道标量s和e，但不知道x或k。但是，Bob知道xG和kG，并且他可以自己计算sG和exG。这意味着他可以检查Alice执行的操作的放大版本的相等性：sG == kG + exG。如果相等，那么Bob可以确信Alice在生成s时知道x。

> **Schnorr身份验证协议中的整数替代椭圆曲线点**
>
> 如果我们可以通过使用简单的整数代替椭圆曲线上的点来创建一个不安全的过度简化版本，可能更容易理解，交互式Schnorr身份验证协议。例如，我们将使用从3开始的质数： 设置：Alice选择x = 3作为她的私钥。她将其与生成器G = 5相乘，以获得她的公钥xG = 15。她将15提供给Bob。
>
> 1. Alice选择私密nonce k = 7，并生成公共nonce kG = 35。她将35提供给Bob。
> 2. Bob选择e = 11，并将其提供给Alice。
> 3. Alice生成 s = 40 = 7 + 11 × 3。她把40给了Bob。
> 4. Bob推导出 sG = 200 = 40 × 5 和 exG = 165 = 11 × 15。然后他验证 200 == 35 + 165。注意，这是Alice执行的相同操作，但所有值都被放大了5倍（G的值）。
>
> 当然，这是一个过度简化的例子。当使用简单整数时，我们可以通过生成器G来除以产品，以获取基础标量，但这并不安全。这就是比特币中使用的椭圆曲线加密的一个关键特性，即乘法容易，但除以曲线上的点是不切实际的。此外，对于这么小的数字，通过穷举法找到基础值（或有效的替代值）是很容易的；比特币中使用的数字要大得多。

让我们讨论一下交互式Schnorr身份验证协议的一些使其安全的特性：

\
**随机数（k）：**&#x20;

在第1步中，爱丽丝选择一个鲍勃不知道且无法猜测的数字，并将该数字的缩放形式kG交给他。此时，鲍勃已经拥有她的公钥（xG），这是她的私钥x的缩放形式。这意味着当鲍勃在处理最终方程式（sG = kG + exG）时，有两个他不知道的独立变量（x和k）。使用简单的代数可以解决一个未知变量的方程，但不能解决两个独立的未知变量，因此爱丽丝的nonce的存在防止了鲍勃能够推导出她的私钥。值得注意的是，此保护取决于nonce在任何方面都不可预测。如果爱丽丝的nonce有可预测的任何东西，鲍勃可能会利用这一点来推断出爱丽丝的私钥。有关更多详细信息，请参阅“签名中随机性的重要性”（第200页）。

**挑战标量（e）：**&#x20;

鲍勃等待收到爱丽丝的公共nonce，然后在第2步中继续给她一个数字（挑战标量），爱丽丝之前不知道并且无法猜测。重要的是，鲍勃只有在她承诺了她的公共nonce之后才会给她挑战标量。想象一下，如果一个不知道x的人想要冒充爱丽丝，而鲍勃在他们告诉他公共nonce kG之前意外地给了他们挑战标量e，会发生什么情况。这使得冒充者可以更改鲍勃将用于验证的方程两侧的参数，sG == kG + exG；具体来说，他们可以同时更改sG和kG。考虑到方程式的简化形式：x = y + a。如果你可以同时更改x和y，你可以使用x' = (x – a) + a来消除a。现在，您选择的任何x值都将满足方程式。对于冒充者而言，实际方程式很简单，他们只需为s选择一个随机数，生成sG，然后使用EC减法选择一个等于kG的kG = sG – exG。他们将给出他们计算出的kG以及稍后的随机sG，而鲍勃会认为这是有效的，因为sG ==（sG – exG) + exG。这解释了协议中操作顺序的重要性：鲍勃必须在爱丽丝承诺她的公共nonce之后才能给她挑战标量。

\
在这里描述的交互式身份验证协议与克劳斯·施诺尔的原始描述部分匹配，但缺少了我们在去中心化比特币网络中需要的两个关键特性。其中之一是它依赖于鲍勃等待爱丽丝承诺她的公共nonce，然后鲍勃给她一个随机的挑战标量。在比特币中，每个交易的支出者都需要由数千个比特币全节点进行身份验证，包括尚未启动但其运营商将来会希望确保他们收到的比特币来自每个交易都有效的传输链的未来节点。无论今天还是将来，任何无法与爱丽丝通信的比特币节点都将无法对其进行身份验证，并且将与对其进行身份验证的每个其他节点不一致。对于像比特币这样的共识系统来说，这是不可接受的。为了让比特币运作，我们需要一个不需要爱丽丝与每个想要对她进行身份验证的节点进行交互的协议。

一种简单的技术，称为Fiat-Shamir变换，可以将Schnorr交互式身份验证协议转换为非交互式数字签名方案。回想一下步骤1和2的重要性，包括它们按顺序执行。爱丽丝必须承诺一个不可预测的nonce；鲍勃必须在收到她的承诺后才给爱丽丝一个不可预测的挑战标量。还记得我们在本书其他部分使用的安全加密哈希函数的属性：当给定相同的输入时，它将始终产生相同的输出，但当给定不同的输入时，它将产生与随机数据无法区分的值。这使得爱丽丝可以选择她的私有nonce，推导出她的公共nonce，然后对公共nonce进行哈希以获得挑战标量。因为爱丽丝无法预测哈希函数的输出（挑战），并且对于相同的输入（nonce），它总是相同的，这确保了爱丽丝即使选择了nonce并对其进行哈希也能得到一个随机的挑战。我们不再需要鲍勃的互动。她只需发布她的公共nonce kG和标量s，每个数千个全节点（过去和未来）都可以对kG进行哈希以产生e，使用它来产生exG，然后验证sG == kG + exG。明确写出，验证方程变为sG == kG + hash(kG) × xG。

我们需要另一个东西来完成将交互式Schnorr身份验证协议转换为对比特币有用的数字签名协议。我们不仅希望爱丽丝证明她知道自己的私钥，还希望她能够承诺一个消息。具体来说，我们希望她承诺与她想要发送的比特币交易相关的数据。有了Fiat-Shamir变换，我们已经有了一种承诺，因此我们可以简单地使其额外承诺消息。现在，我们不仅使用hash(kG)，还使用hash(kG || m)来承诺消息，其中||表示串联。

现在我们已经定义了schnorr签名协议的一个版本，但还有一件事情我们需要做来解决比特币特定的问题。在BIP32密钥派生中，如在第92页的“公共子密钥派生”中描述的，非硬化派生的算法将一个公钥和一个非秘密值相加，以产生一个派生的公钥。这意味着也可以将那个非秘密值添加到一个密钥的有效签名中，以产生一个相关密钥的签名。这个相关签名是有效的，但它不是由持有私钥的人授权的，这是一个重大的安全失败。为了保护BIP32非硬化派生，并且支持人们希望在schnorr签名之上构建的几个协议，比特币的schnorr签名版本，称为secp256k1的BIP340 schnorr签名，还承诺要使用的公钥，除了公共nonce和消息之外。这使得完整的承诺哈希(kG || xG || m)。 现在我们已经描述了BIP340 schnorr签名算法的每个部分，并解释了它为我们做了什么，我们可以定义该协议了。整数的乘法是模p执行的，表示操作的结果被数字p除（如在secp256k1标准中定义的）并且余数被使用。数字p非常大，但如果它是3并且操作的结果是5，则我们实际使用的数字是2（即，5除以3的余数为2）。 设置：爱丽丝选择一个大的随机数(x)作为她的私钥（直接选择或使用BIP32等协议从大的随机种子值确定性地生成私钥）。她使用secp256k1中定义的参数（参见第56页的“椭圆曲线密码学解释”）将生成器G乘以她的标量x，产生xG（她的公钥）。她将她的公钥提供给以后将验证她的比特币交易的所有人（例如，通过在交易输出中包含xG）。当她准备花费时，她开始生成她的签名：

* Alice选择一个大的随机私用nonce k 并派生公共nonce kG。
* 她选择她的消息 m（例如，交易数据）并生成挑战标量 e = hash(kG || xG || m)。
* 她生成标量 s = k + ex。两个值 kG 和 s 是她的签名。她将这个签名提供给所有想要验证该签名的人；她还需要确保每个人都收到她的消息 m。在比特币中，这是通过将她的签名包含在她的支付交易的见证结构中，然后将该交易中继到完整节点来完成的。
* 验证者（例如完整节点）使用 s 派生 sG，然后验证 sG == kG + hash(kG || xG || m) × xG。如果等式成立，则Alice证明了她知道自己的私钥 x（而没有泄露它），并承诺了消息 m（包含交易数据）。