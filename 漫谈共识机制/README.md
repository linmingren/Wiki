# WIKI：漫谈共识机制
**作者：fisco-dev**  

共识机制是区块链领域的核心概念，无共识，不区块链。

区块链作为一个分布式系统，可以由不同的人或机构，将安装了区块链软件的计算机（简称节点）加入到网络里，然后共同计算数据、共同见证交易的执行过程，并确认最终计算结果。

如何协同这些松散耦合，互不信任的节点一起工作，达成信任关系，并保障一致性，持续性，可以抽象为“共识”过程。

共识需要解决的几个核心问题是：  
1. 谁在这个网络里有记账权，也就是做为leader发起一次记账。    
2. 做为互相不信任的参与者，为什么要采纳和相信某一个人给出的记账。  
3. 怎么保证大家最终收到的结果都是一致的，无错的。    

公链上把“激励”也作为一个核心的考虑项，来保证链的可持续发展，这是合理的，但是联盟链不一定会采用代币激励，所以本文不把激励放到这三个核心问题里。

以下介绍几个典型共识的策略：  
首先介绍 PoW（Proof Of Work）工作量证明。POW是比特币采用的共识算法，从诞生起运行至今，表现稳健，是史上最成功的共识算法，没有之一。  
POW的哲学简单可靠，大家理论上都可以发起记账，拼算力，看谁能先算出一个小概率的随机数，也就是俗称的挖矿，这个随机数在数学角度有严谨的推演，通过动态调整的难度策略限制，无论矿工投入的硬件多强，都能控制在10分钟左右挖到一个矿，算力强的矿工因为可以在这段时间内计算更多的随机数，所以更有机会比其他人先挖到矿。  
挖到矿的矿工就可以广播自己的记账结果，全网的其他节点可以选择接受这个结果，然后默默开始的下一轮的挖矿。挖到矿的矿工同时得到一笔算法赋予的奖励，也就是一些比特币。  
如果刚好两个矿工完全同时的挖到矿，那么就会出现竞争，网络上出现了两个记账结果，这个时候其他节点会随机选择一个，或者按顺序选择自己先接收到的一个，继续在它基础上进行挖矿（基于该记账结果的基础，再挖到的新的一次矿，称为新一次确认），由于网络有随机延迟，随机策略等区别，一般会有一个记账结果会被更多人接受，有机会更快的被持续确认六次，另一个就被抛弃了。这就是竞争和分叉处理。  
POW的表现有点像单纯的原始年代，大家都凭力量和速度去挖矿淘金，谁先挖到一块金子，就胜出一次，看起来非常的公平公正，简单粗暴，无可挑剔，其简单性也让这个体系得以稳定运行多年。毕竟拼了老命去挖矿的矿工，只会继续拼命挖矿来加固自己的成果，轻易不会作弊，矿工作弊会导致网络失去公信力，其辛苦挖来的持有的资产也会贬值。其他人想攻击这网络，需要投入的算力要比现有的矿工多，得比现有的已经很熟练很有力量的矿工更努力，如果收益不是很可观，是不值得的 -- 换句话说，反正比特币网络有奖励，如果我有这超越了其他矿工算力，为何不去挖矿赚钱，破坏网络吃力不讨好干啥（除非别有所图）。于是，博弈论就这么玄妙的产生了作用。  
同时，这种掰腕子秀肌肉的哲学，最让人吐槽的就是不环保，能耗太多，效率较低，无脑算HASH，对科学计算也没什么贡献（曾经有将算力用于计算天文问题来挖矿的方案，但是没有形成大规模效应），参与者能通过暴力堆积硬件，采用极致优化（只能做HASH计算的）芯片，在网络里掌握话语权，也就是所谓的算力集中问题。  

总结一下，之前的三个问题在POW里的解决方案  
1. 谁在这个网络里有记账权，也就是做为leader发起一次记账。  
---虽然大家都有机会参与记账，算力强大的人更有机会成为记账者，谁劲儿大谁当擂主。  
2. 做为互相不信任的参与者，为什么要接受和相信某一个人给出的记账。   
---因为记账者付出了算力的巨大代价做为背书，他可以为记账行为负责，可以倾向认为他不作恶。  
3. 怎么保证大家最终收到的结果都是一致的，无错的。  
---其他人可以从网络里同步到记账者的数据，通过区块里的hash算法校验数据，因为区块数据有难以产生（需要算力），容易校验（hash容易计算）的特性，大家对数据校验通过后都采用记账者给出的数据，全网一致，如有分叉，6次确认后解决。

考虑到POW的短板，行业里提出了PoS（Proof Of Stake）权益证明的思路。PoS的基本思想是让在网络中拥有更多权益的人有机会在更短时间里做更多的决定，因为毕竟权益持有者人更倾向维护网络的利益，且害怕作恶后被惩罚，损伤自己的声望和财产。  
PoS的算法实现有很多变种，其中一个比较典型的是引入了“币龄”的概念，比如，一个人手里拿了一笔钱，而且持续拿了一段时间，那么基本上可以认为这个人是比较忠于这个网络的，他的权益值就比较高了。为了鼓励人们持有代币，一般是有利息可图的，所以也鼓励了人们倾向持有资产，不断增值，维持权益。  
对权益较高的人，如何获得记账权力，也有多个变种实现，在继续采用挖矿算法的网络里，可以加权的降低他的挖矿难度，让他更容易挖到矿，获得记账权。或者采用加权的轮询算法，轮流让权益拥有者轮流记账。  
可以认为，PoS引入了财富做为公信力背书的一个考虑角度，和简单粗暴谁有力量谁说了算的PoW对比，有利于降低纯计算资源的消耗，看起来也没有那么肌肉感了。  
  
总结一下PoS：   
1. 谁在这个网络里有记账权，也就是做为leader发起一次记账。    
---持有权益较多的一拨人竞争或轮流记账。  
2. 做为互相不信任的参与者，为什么要接受和相信某一个人给出的记账。    
---记账者用自己的财力做为背书，大家相信他们不会轻易作恶，如果作恶，可以用经济方式惩罚他。  
3. 怎么保证大家最终收到的结果都是一致的，无错的。    
---通过验证记账权益和数据，大家相信这一轮的记账者，一致使用他的记账结果    。

在PoS的基础上，又发展出了一个DPoS的共识算法。和PoS对比多了D，全称是“Delegated Proof of Stake，股份授权证明机制”，原理是让所有持币人都有机会选出自己的代表，比如全网有1万个参与人，通过一定的算法，参与人以自己的代币为权益证明，选出101个代表，这些代表可以轮流或者采用PoS算法加权的获得记账权，进行记账。
DPoS理论上不要求选出的代表个体本身是权益所有人，看起来更民主，更开放。网络参与者做为选民有机会选出自己的代表，来给自己的利益代言。如果选出的代表不作为（轮到自己记账时不记账），或者作恶，可以把他们踢掉，如有必要进行惩罚（选民们也有可能被惩罚）。否则记账者有机会获得相应的奖励，也有可能将奖励发放给选出自己的民众们。

总结一下DPoS：  
1. 谁在这个网络里有记账权，也就是做为leader发起一次记账。  
---拥有权益的散户，分别选出自己的代表参与记账。  
2. 做为互相不信任的参与者，为什么要接受和相信某一个人给出的记账。    
---大家相信代表，因为他们能代表相当一部分人的在网络里的利益。  
3. 怎么保证大家最终收到的结果都是一致的，无错的。  
---相信代表，一致使用他的记账结果 。   

以上的都是在公有链里采用的共识算法，都能解决可信和一致性的问题，区别在于组织方式和计算代价不同，有一点相同的是：都是追求“**最终一致性**”，而不是强一致性。  
也就是说在网络中，由于网络故障、通信分区、权益和算力相近等情况，依旧可能有多个记账者在竞争记账，所以每次记账可以理解成一次提案，大家暂时保存这一次提案的竞争结果，最终确定下来可能要依赖多次竞争，也就是所谓的一顿烧烤解决不了的问题，那就再来一顿，然后再一顿。直到大家看到，之前的某一次记账，都被大家接受了，而且是接受了好几次，才达成**最终一致性**，这个时间是相对比较长的，比如比特币的6次确认，需要一个小时。公链里的共识以及其他交易行为，可以理解为一种概率游戏，随着时间推移和投入到共识里的工作量或权益越多，确定性的概率就越大，大到概率上几乎不可逆，或者必须投入匪夷所思的大量的资源才能推翻时，就达成了确定性。

我们的目标是实现联盟链的共识，联盟链和公有链不同的是，参与记账的人，身份是可知的、可控的，可能有监管或者经济共同利益之类的措施来约束他们。

另外，联盟链的场景倾向与追求强一致性，也就是说一笔交易发生了，就不能再被分叉或回滚推翻了。所以联盟链通常使用的是PBFT和Raft算法以及其变种。

BFT（Byzantine Fault Tolerance）即“拜占庭容错算法”，其细节说起来特别复杂，来自80年代的一个理论，据说至今没有一个完全的、普适性的权威实现，Babara Liskov在2002年提出PBFT，放松了约束来解决拜占庭问题，而因此获得了图灵奖。我们参考“实用性的拜占庭容错” Practical BFT做了一个实现，是自己从头开发的。

PBFT的记账者在初始阶段就是相对固定的，联盟链里可以根据不同的场景来选择记账者，如机构之间线下协商、线上配置，也可以引入DPoS的思想，先通过一些选举策略选出一批记账者，但这样在联盟链里可能带来额外的复杂性，如果商业场景需要，可以在既有基础上开发PBFT-dPos。

所以我们的实现是可动态配置式的记账者列表，在具备共同利益的的联盟链网络里，所有或大部分机构都参与记账，大家一起维护商业利益，如果某个机构在记账过程中犯错、作弊、或者不工作，都可以通过少数服从多数容错，然后进行事后追责，保护大家的利益。算法的容错能力是能接受1/3成员的错误和作弊，如7个成员，只要作恶或出错的成员在两个和两个以下，网络就是安全的。但如果网络里有很多个作恶者，那么这个网络可被视为失效。

BFT算法的过程是一次提案，几步提交，在这个过程中有复杂的状态机维护的细节。起始时可按一些简单的规则，如轮次机制，让某一个机构的某一个节点在某个区块高度上，成为议长，得到记账权，然后进行记账提案，其他节点对提案进行数据验证（验证签名，验证交易数据，验证合约计算结果等），觉得都ok之后，返回一个签名确认，议长收集大家的签名，达到一定的数量后，认为达成少数服从多数的效果，则将记账结果广播出去，大家在将记账结果存下来之前，还可以再进行一次投票和计票，确认大多数人都收妥了记账数据并无异议，再将记账数据存下来，开始下一轮。

可以形象的描述下：一个团体，比如有7个人，大家都有投票权，要对某次聚餐计划的选择进行投票，这个时候某一个人发起一个提议，大家看ok呀，纷纷举手，提议的人定时数数，举手的人超过4个，就达成决议，否则再换一个人提议。如果提议达成，决定了吃什么什么时候吃，发起人给大家群发个邮件制定日程，大家接受日程并给出反馈，当看到有不少人都接受了邮件日程不会反悔，那么，这顿饭就成行了。

如果团队人数较多，也是可以让一部分人成为投票代表，比如一个上百人的班里，由选出来的班干部投票就可以了。其他人成为“观察者”，无条件接受这一部分人通过拜占庭容错算法得出的结果，和DPoS的思想类似。

PBFT算法在参与人数较少的联盟链里，有相当可观的实用价值：  
1. 少数服从多数，体现了民主性。  
2. 大家参与投票，都能刷存在感。  
3. 不需要粗暴的算力介入，相当环保。  
4. 多步提交反复确认，一旦共识完成就达成一致性，可以短时间内就达成确定性的交易。  
5. 每次投票都有数字签名，可以抗抵赖，不会投了票不认。  
6. 可以不依赖代币经济鼓励来保证记账的正确性。

所以，PBFT相对公链的各种共识，可以理解成特定的已知身份的团队内的快速达成一致的一种高效算法，特定团队的形成也是至关重要的，所以会有PBFT-Auth(经过验证的记账人), PBFT-PoS,PBFT-DPoS等多种变化。是否需要在投票和容错的部分进行常场景性的优化（如带加权系数的投票，容错由1/3改为1/2或者改为0容错等等），要看场景需求，目前来看，保证有一个相对简单，运行稳定的PBFT算法是首要的。

总结一下PBFT：  
1. 谁在这个网络里有记账权，也就是做为leader发起一次记账。  
--所有人平等的轮流参与记账，或者经过许可的，或者大家选出来的一拨人进行轮流记账。  
2. 做为互相不信任的参与者，为什么要接受和相信某一个人给出的记账。   
---少数服从多数，每次记账的结果都是多数派的投票胜利。   
3. 怎么保证大家最终收到的结果都是一致的，无错的。   
---通过多步提交反复确认，保证提案和最终结果都是正确的，是多数人承认的，是全网一致的。  

PBFT的小问题在于状态机维护复杂，投票往返步骤较多，容易受网络波动影响，对网路延迟和丢包相当敏感，当参与者数量增多，不稳定因素会被放大，其稳定性和效率可能会显著下降，这也是我们在持续的踩坑和优化的部分，要做到一个企业级环境可用的，健壮的PBFT共识，是非常有挑战性的，不但需要对区块链的数据和运作哲学有很深的理解，也要对分布式系统的通信，协作，性能优化有大量的实践经验。

结合以太坊帐户模型和智能合约引擎机制，我们对PBFT也做了大量的优化，比如把串行的任务改成并行，把不必要重复计算的数据（如区块数据打包，签名等）缓存下来，快速侦测记账者存活，加快切换速度等等，都是复杂的工程，需要一次又一次迭代的长夜漫漫的脑补、实现、测试、修BUG、压测、量化，足以重新写一篇长文了。

联盟链还可以采用一种共识算法Raft，简单的说可以理解成大家选出一个记账者，如果它稳定运行没有挂掉，就由他记账，大家无条件接受他的记账结果，相信他是诚实的，如果他挂掉了，那么大家是可以通过超时或网络探测感知，然后快速启动一轮投票，来选出一个新的记账者，然后继续无条件的等它记账，这样就达成了容错性。

这在信任度较高，机构组成简单的的联盟链或者一个机构内的私有链里，是比PBFT更加高效的一种做法，因为不需要多步的反复确认，受网络影响的可能性也小很多。

但是Raft算法并不解决记账者耍花样的问题，如果选出的记账者作恶，其他人当时是没有办法识破的，最多只能通过事后检查来发现，在确定性要求较高的场景，比如收了钱就要交货的DVP场景里，容易出现钱货两空的情况。另外，Raft算法有可能在网络波动或竞争情况下出现短暂的网络分叉，需要多次确认。

Raft适合用于专注解决可用性（保持系统稳定运行），追求相对较高效率（比优化后的PBFT大致高20~50%的效率）的场景，基本忽略欺诈可能，且对交易延时要求可以放宽（等待多次确认）。

为了在速度和抗欺诈之间获得平衡，也有出现PBFT-Raft混用的共识，比如让一个记账者固定出块，其他人极少轮次的投票，或者进行快速的后置检测，如果发现Raft的记账者在作恶，立刻回滚冲正，并踢掉并惩罚作恶的记账者等等，这就有一些场景化的需求需要考虑了。

总结一下Raft：  
1. 谁在这个网络里有记账权，也就是做为leader发起一次记账。  
--选出一个记账者，让他做为唯一的可信者进行记账，除非挂掉。  
2. 做为互相不信任的参与者，为什么要接受和相信某一个人给出的记账。  
---无条件相信记账者，from the beginning till the last,至死不渝。  
3. 怎么保证大家最终收到的结果都是一致的，无错的。  
---记账者说的都是对的，大家都用他的数据，是一致的。  

**回顾一下：  
POW：用尽创世纪时代的洪荒之力求生存求发展。  
PoS：采用权益加权，减少计算资源消耗。  
DPoS：一定程度的民选制度，用民意取代集中的权益，民意代表们把握话语权。  
PBFT：少数服从多数，快速达成共识，体现参与度和民主性，有较强的确定性和抗欺诈性。  
Raft：强调可用性和最终一致性，效率较高，不强调抗欺诈性。**  

共识算法的研究是很有意思的课题，我们也看到共识算法的选择和演进，隐约和人类社会的不同阶段是对应得上的。算法其实是社会里人和财产的关系在计算机世界的一种映射，研究共识算法，不仅是需要研究计算机理论（图灵机时代，其实计算机也能体现人性），也要去深刻的理解社会学，经济学，心理学，博弈论。  
毕竟，在一个不完全互信的环境里，宛如黑暗森林，需要复杂的算法来协同、斡旋各怀心事的参与者，并且能惩罚作恶的、奖励诚实的，目前社会制度其实也没有完美的做到，一切努力只是尽量的使系统运作的尽量顺畅，让世界更好一点点。

