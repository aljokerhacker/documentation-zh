# GreatVoyage-v4.5.1(Tertullian)
GreatVoyage-v4.5.1(Tertullian)版本引入多个重要的优化更新，优化的交易缓存加载流程，加快了节点启动速度；优化的获取区块逻辑和轻节点同步逻辑，提升了节点的稳定性；优化的账户资产结构和TVM存储结构，提升了交易的处理速度，从而进一步提高了节点的性能；支持prometheus协议接口为用户带来更便捷的开发体验，有助于进一步繁荣波场生态。


# 核心协议
## 1. 优化交易缓存的加载
GreatVoyage-v4.5.1(Tertullian)之前版本，从节点启动到区块同步需要较长的时间，其中交易缓存的加载占用了大部分的节点启动时间。交易缓存被节点用来判断一个交易是否是重复的交易，因此在节点启动过程中，需要将交易缓存从数据库加载到内存，而在GreatVoyage-v4.5.1(Tertullian)之前的版本中，交易缓存的加载是以交易为存储单元进行数据库读取的，因此读取的数据量较大，整个读取过程较耗时。

为了加快节点启动速度，GreatVoyage-v4.5.1(Tertullian)版本优化了交易缓存的加载方式，通过以区块为存储单元进行数据库读取，减少了数据库读取的次数，提高了交易缓存加载的效率，提升了节点启动的速度。


TIP: https://github.com/tronprotocol/tips/blob/master/tip-383.md
源代码: https://github.com/tronprotocol/java-tron/pull/4319 

## 2. 优化账户TRC-10资产存储结构
在GreatVoyage-v4.5.1(Tertullian)之前的版本中，当账户中的TRC10资产过多时，账户在数据库中存储的内容很大， 导致在执行交易的过程中，账户的反序列化操作非常耗时，因此，GreatVoyage-v4.5.1(Tertullian)版本增加了一个优化账户资产结构的新提案，允许将TRC-10资产从账户中分离出来，并单独存储在一个key-value数据结构中，以减少账户结构中的内容，加快账户的反序列化操作，减少交易的执行时间，从而提高网络吞吐量，提升网络性能。

TIP: https://github.com/tronprotocol/tips/blob/master/tip-382.md 
源代码：https://github.com/tronprotocol/java-tron/pull/4392 

## 3. 优化轻节点同步逻辑
由于轻节点不保存完整的区块数据，因此，某个节点所连接的轻节点中有可能没有它想要与之同步的区块，这时所连接的轻节点会主动断开连接。在GreatVoyage-v4.5.1(Tertullian)之前的版本中，节点有可能重复的与这样的轻节点建立连接，然后被对方断开连接，这极大的影响了节点间同步区块的效率。因此，在GreatVoyage-v4.5.1(Tertullian)版本中，优化了节点建立连接的逻辑，在节点间握手消息中加入了”节点类型”和”节点的最低区块”两个字段，并且节点会保存与各个节点的握手消息。如果当前节点的最高区块低于轻节点最低区块时，它将主动与该轻节点断开连接，并且下次再与节点建立连接时，会过滤掉这样的节点，避免了更多的无效连接，提高了节点间同步的效率。


TIP: https://github.com/tronprotocol/tips/blob/master/tip-388.md 
源代码: https://github.com/tronprotocol/java-tron/pull/4323  



## 4. 优化区块广播逻辑
GreatVoyage-v4.5.1(Tertullian)版本优化了区块广播逻辑，使fast farward节点只将区块广播到接下来将要出块的三个超级代表节点（广播到的超级代表节点的数量可以通过配置文件更改），以保证超级代表节点可以及时的获取到最新区块，提高了区块生产的效率。

源代码：https://github.com/tronprotocol/java-tron/pull/4336 

## 5. 优化区块获取逻辑
由于网络原因，节点有可能接收不到广播的新区块，在GreatVoyage-v4.5.1(Tertullian)之前的版本中，当获取区块超时后，节点将通过P2P同步流程来获取该区块，但是同步流程比较复杂并且耗时，因此，GreatVoyage-v4.5.1(Tertullian)版本优化了获取最新区块的流程，节点将首先根据各个节点的状态选择一个节点，然后直接发送区块获取消息`FetchInvDataMessage`给该节点，来获取最新区块，这节省了区块同步过程中的大部分时间，加快了最新区块获取的速度，提高了节点的稳定性。

TIP: https://github.com/tronprotocol/tips/blob/master/tip-391.md 
源代码：https://github.com/tronprotocol/java-tron/pull/4326 

## 6. 支持prometheus协议接口
从GreatVoyage-v4.5.1(Tertullian)版本开始，节点提供开源的系统监控工具prometheus协议接口，用户可以更方便的监控节点的健康状态。

TIP: https://github.com/tronprotocol/tips/blob/master/tip-369.md 
源代码：https://github.com/tronprotocol/java-tron/pull/4337 

## 7. 节点支持在特定状态下停止运行
为了方便节点部署者进行数据备份或数据统计，从GreatVoyage-v4.5.1(Tertullian)版本开始，节点支持在特定的条件下停止运行，用户可以通过节点配置文件设置节点停止的条件，如节点停止的区块时间，区块高度，节点从启动到停止需要同步的区块数量。在满足设置的条件时，节点将停止运行。

TIP: https://github.com/tronprotocol/tips/blob/master/tip-370.md  
源代码：https://github.com/tronprotocol/java-tron/pull/4325 


# TVM
## 1. 调整TVM最大执行时间可设置的上限
“TVM最大执行时间”是TRON网络的一个动态参数，表示允许一笔智能合约执行的最大时间，超级代表可以通过提案投票来更改此参数，在GreatVoyage-v4.5.1(Tertullian)之前的版本中，该参数可修改的最大值是100ms。而随着TRON网络基础设施的稳定和生态的蓬勃发展，100ms上限一定程度上限制了智能合约的复杂性。因此，GreatVoyage-v4.5.1(Tertullian)版本增加了一个新的提案，允许将”TVM最大执行时间”的可设置上限提升到400ms。

TIP: https://github.com/tronprotocol/tips/blob/master/tip-397.md  
源代码：https://github.com/tronprotocol/java-tron/pull/4375 
## 2. 优化TVM缓存结构

在GreatVoyage-v4.5.1(Tertullian)之前的版本中，TVM中的缓存数据是以字节数组的形式存储的，当需要对缓存中的数据进行更改时，首先要将数据从字节数组的形式进行序列化操作变为protobuf对象，然后更改对象的某个字段（如账户余额等）生成一个新的对象，然后再对新生成的protobuf对象进GreatVoyage-v4.5.1(Tertullian)版本优化了TVM执行时缓存中的数据结构，直接保存protobuf对象数据，以减少访问缓存中数据时的序列化/反序列化操作，加快TVM执行字节码的速度。


源代码：https://github.com/tronprotocol/java-tron/pull/4375   


--- 

*Hope is patience with the lamp lit.* 
<p align="right"> --- Tertullian </p>