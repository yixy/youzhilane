# 不经意传输OT #

Oblivious Transfer（不经意传输）简称OT，是一种基本密码学原语，被广泛的用于安全多方计算等领域。

>OT最早在1981年被 Michael O. Rabin提出[1]，在Rabin的OT协议中，发送者S发送一个信息m给接收者R，接收者R以1/2的概率接受信息m。所以在协议交互的结束的时候，S并不知道R是否接受了消息。该方案是基于RSA加密体系构造的。
>1985年S. Even, O. Goldreich, and A. Lempel提出了1-out-2 OT[2]，在新的方案中S每次发送2个信息m0 m1，而R每次输入一个选择b。当协议结束的时候，S无法获得关于b的任何有价值的信息，而R只能获得mb，对于m1−b，R也一无所知。
>1988年，Claude Crépeau 证明了Rabin的OT方案和1-out-2 OT方案是等价的[3]。
>在1986年Brassard等人首次将1-out-2 OT扩展为1-out-n OT[4]，1998年Stern J P.首次将公钥特性加入了1-out-n OT协议之中[5]。
>2001年Naor和Pinkas基于Diffie-Hellamn（DDH）困难问题假设给出了一个高效的2轮1-out-n OT协议[6]，同一年Aiello等人基于同态加密也给出了一个2轮的1-out-n OT协议[7]，并且在该方案中无论n多大，R只需要进行2次指数运算而S则需要2n 2n2n次指数运算。
>2003年Yuval Ishai等人以1-out-2 OT为基础提出了一种高效的通过少量OT构造大量OT的方案，该方案基于随机神谕机构造[8]。
>2008年，Lindell将cut-and-choose技术融入到OT协议中，给出了一个高效而且可以完全模拟的OT协议[9]。
>2013年，Vladimir Kolesnikov1 和 Ranjit Kumaresan直接构造了1-out-n OT[19]，并且对于短消息的茫然传输效率比03年Yuval Ishai等人的方案更高。

[1]Michael O. Rabin. “How to exchange secrets by oblivious transfer.”
[2]S. Even, O. Goldreich, and A. Lempel, “A Randomized Protocol for Signing Contracts.”
[3]Claude Crépeau. “Equivalence between two flavours of oblivious transfer.”
[4]Gilles Brassard, Claude Crépeau and Jean-Marc Robert. “All-or-nothing disclosure of secrets.”
[5]Stern J P. “A New and Efficient All-Or-Nothing Disclosure of Secrets Protocol.”
[6]Moni Naor and Benny Pinkas. “Efficient oblivious transfer protocols.”
[7]Bill Aiello, Yuval Ishai, and Omer Reingold. “Priced Oblivious Transfer:How to Sell Digital Goods.”
[8]Ishai Y., Kilian J., Nissim K., Petrank E. “Extending Oblivious Transfers Efficiently.”
[9]Lindell Y. “Efficient Fully-Simulatable Oblivious Transfer.”
[10]Vladimir Kolesnikov and Ranjit Kumaresan. “Improved OT Extension for Transferring Short Secrets.”

## 参考 ##

https://blog.csdn.net/jason_cuijiahui/article/details/89762417