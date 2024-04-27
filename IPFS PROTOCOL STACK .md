# IPFS PROTOCOL STACK 
### 身份层
#### 管理节点身份生成和验证  
+ 身份层源码-nodeId生成  
``` 
    difficulty=<integer parameter>
    n=Node{}
    do{
        n.PubKey, n.PrivKey=PKI.genKeyPair()
        n.NodeId=hash(n.PubKey)
        p=count_preceding_zero_bits(hash(n.NodeId))
    }while(p<difficulty)
```
+ 身份层源码-node结构
```
type NodeId Multihash
type Multihash []byte
type PublicKey []byte
type PrivateKey []byte

type Node struct{
    NodeId NodeID
    Pubkey PublicKey
    PriKey PrivateKey
}
```
### 网络层
#### 管理与其他节点的连接，使用多种底层网络协议
+ 传输:IPFS兼容现有的主流传输协议，其中有最适合浏览器端使用的WebRTC DataChannels,也有UTP(LEDBAT)等
+ 可靠性: 使用uTP或sctp来保障。
+ 可连接性: 使用ICE NAT网络穿透技术来实现广域网的可连接性
+ 完整性: 使用哈希校验检查数据完整性。
+ 可验证性: 使用数据发送者的公钥及HMAC消息认证码来检查消息的真实性。
### 路由层
#### 以分布式哈希表(DHT)维护路由信息以定位特定的对等节点对象。响应本地和远端节点发出的查询请求
+ IPFS的路由实现了3 种基本功能: 内容路由、节点路由及数据存储。
### 交换层
#### 一种支持有效块分配的新型交换块协议(BitSwap),模拟可信市场，弱化数据复制，防作弊。
+ IPFS 中的 BitSwap 协议是协议实验室的一项创新设计，其主要功能是利用信用机制在节点之间进行数据交换。
+ Bitswap数据结构
```
type BitSwap struct {
ledgers map[Nodeld]Ledger   // 节点账单
active  map[Nodeld] Peer    // 当前已经连接的节点
need list[]Multihash        // 此节点需要的块数据校验列表
have list IMultihash        // 此节点已收到的块数据校验列表
```
### 对象层
#### 具有基于Merkel DAG所构建的对象层，具有内容寻址、防冗余特性。
+ IPFS使用Merkle DAG技术构建了一个有向无环图数据结构，用来存储数据对象
+ Merkle DAG 的对象结构定义
```
type IPFSLink struct {
    Name string // 此link的别名
    Hash Multihash //目标的加密Hash
    Size int//目标总大小
}
type IPFSObject struct{
    links []IPFSLink // links数组
    data []byte // 不透明内容数据
} 
```
+ ipfs加密对象结构定义
```
type EncryptedObject struct{
    Object []byte // 已加密的原始对象数据
    Tag []byte // 可选择的加密标识
} 
type SignedObject struct{// 已签名的原始对象数据
    Object []byte //HMAC签名
    Signature []byte
    PublicKey []MultiHash // 多重哈希身份键值
}
```
### 文件层
#### 类似Git的版本化文件系统，支持blob、commit、list、tree等结构
+ IPFS定义了一组对象，用于在merkle DAG之上对版本化文件系统进行建模。
```
1.块(block):一个可变大小的数据块
2.列表(list):一个块或其他列表的集合
3.树(tree):块、列表或其他树的集合
4.提交(commit): 树版本历史记录中的快照
```
### 命名层
具有自验证特性的可变系统

