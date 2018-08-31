# Casper+Sharding chain v2.1
###### 译者：程阳
原文：https://notes.ethereum.org/SCIg8AH5SA-O4C1G1LYZHQ

### 工作正在进行中！！！

这是描述Casper + Sharding链第2.1版规范的工作进度文档。

在这个协议中，有一条中心PoS链存储和管理当前活跃的PoS验证者们。成为一名初始验证者唯一可用的机制是在现有的PoW主链上发送一条包含32ETH的交易。当你这样做了，PoS链一处理区块，你将会处在一个队列中，并最终以一名活跃的验证者正式加入，直到你自愿退出，或者因异常行为被处罚而被强制注销。

PoS链上的主要负载源是认证（attestations）。一个认证包括两方面角色：
1. 证实信标区块（beacon chain）中的一些父区块
2. 证实一个区块在分片（shard）中的哈希（足够多这样的证明会创建一个交联（crosslink），来确保分片中的区块进入主链）

每个分片（总共可能有1024个分片）都是一个PoS链，分片链存储交易和账户信息。交联提供“确认”分片中的片段进入主链的服务，同时也是不同分片之间进行通信的主要方式。

值得一提的是，还可以考虑一种更简单的“最小切分算法”，其中交联只是提交的数据块的散列，这些数据块本身并不以任何方式相互链接。

注意： 在[https://github.com/ethereum/beacon_chain](https://github.com/ethereum/beacon_chain) 的python代码和 [an ethresear.ch post](https://ethresear.ch/t/convenience-link-to-full-casper-chain-v2-spec/2332)没有反映最新的更改。如果有人么差异，这个文档很可能会反映出最新的变化。

##### 术语：
- 验证者（Validator）—— Casper/分片共识系统的参与者。通过存32ETH到Casper机制中就能成为一名验证者。

- 活跃验证者集合（Active validator set）—— 那些正在参与的验证者，同时也是Casper机制寻求产生以及证明区块，交联和其他共识对象的验证者。

- 委员会（Committee）—— 从活跃验证者集合中（伪）随机抽样的子集。当一个委员会被集体提及时，如“该委员会证明X”，这种假定为“该委员会的某个子集包含足够的验证者，该协议承认其代表委员会”。

- 提案者（Proposer）—— 创建一个区块的验证者

- 证明者（Attester）—— 在区块上签名的委员会中的一名验证者

- 信标链（Beacon chain）—— 中心PoS链是分片系统的基础

- 分片链（Shard chain）—— 进行交易和存储账户数据的链之一

- 交联（Crosslink）—— 来自委员会的一组签名，证明一个块在分片链中，（签名）可以包含在信标链中。交联是信标链“了解”分片链更新状态的主要手段。

- 时间槽（Slot）—— 每8秒一个周期，在一个周期里提案者可以创建一个区块，证明者可以去做验证

- 改朝换代（Dynasty transition）—— 更换验证者集合

- 朝代（Dynasty）—— 创世以来，在一个给定的链中改朝换代的次数

- 时代（Cycle）—— 所有验证这都有一次机会进行验证的一组块

- 最终化，合理（Finalized, justified） —— 参见Casper FFG终稿https://arxiv.org/abs/1710.09437

##### 常量：
- SHARD_COUNT —— 分片数量的常量。现在设置的是1024

- DEPOSIT_SIZE —— 32ETH

- MAX_VALIDATOR_COUNT —— 2<sup>22</sup> = 4194304 # 注意：这意味着在同一时间最多只能下注1.34亿ETH
译者注：4194304 * 32 ETH = 134217728 ETH

- SLOT_DURATION —— 8秒

- CYCLE_LENGTH —— 64个时间槽

- MIN_COMMITTEE_SIZE —— 128（理论基础：参见这里推荐的最小值111 [https://vitalik.ca/files/Ithaca201807_Sharding.pdf](https://vitalik.ca/files/Ithaca201807_Sharding.pdf)）

##### PoW 主链的改变：
本PoS/分片提案可以独立于现存的PoW主链实施。主链只需要做两处变更（且第二个在技术上讲不是严格必要的）。

- 在主链上添加一个合约。该合约允许你存```DEPOSIT_SIZE```(32)个ETH。```deposit```函数接收5个参数，分别是（i）```pubkey```(bytes)，(ii)```withdrawal_shard_id```(int)，(iii)```withdrawal_addr```(address)，（iv）```randao_commitment```(bytes32)，(v) ```bls_proof_of_possession```
- 主链客户端将实现一个方法，```prioritize(block_hash,value)```。如果该区块可用并得到验证，该方法将（区块）分数设定为给定值，并递归调整所有后代的分数。这允许PoS信标链的终结小工具也能隐式地最终化主链区块。注意，将该方法实施到PoW客户端是对PoW分叉选择规则的更改，因此也是一种分叉。

##### 信标链
信标链是PoS系统中的“主链”。信标链的主要职责是：
- 存储和维护活跃的，处于排队中和退出的验证者集合
- 处理交联（参见前面）
- 逐块处理共识，以及FFG终结小工具

这是进入每个信标链的区块的字段：
```
fields = {
    # Hash of the parent block
    'parent_hash': 'hash32',
    # Slot number (for the PoS mechanism)
    'slot_number': 'int64',
    # Randao commitment reveal
    'randao_reveal': 'hash32',
    # Attestations
    'attestations': [AttestationRecord],
    # Reference to PoW chain block
    'pow_chain_ref': 'hash32',
    # Hash of the active state
    'active_state_root': 'hash32',
    # Hash of the crystallized state
    'crystallized_state_root': 'hash32',
}
```

信标链状态被分为两部分，活跃状态和结晶状态。
这是 ```ActiveState```：
```
fields = {
    # Attestations that have not yet been processed
    'pending_attestations': [AttestationRecord],
    # Most recent 2 * CYCLE_LENGTH block hashes, older to newer
    'recent_block_hashes': ['hash32']
}
```

这是 ```CrystallizedState```：
```
fields = {
    # List of validators
    'validators': [ValidatorRecord],
    # Last CrystallizedState recalculation
    'last_state_recalc': 'int64',
    # What active validators are part of the attester set
    # at what slot, and in what shard. Starts at slot
    # last_state_recalc - CYCLE_LENGTH
    'indices_for_slots': [[ShardAndCommittee]],
    # The last justified slot
    'last_justified_slot': 'int64',
    # Number of consecutive justified slots ending at this one
    'justified_streak': 'int64',
    # The last finalized slot
    'last_finalized_slot': 'int64',
    # The current dynasty
    'current_dynasty': 'int64',
    # The next shard that crosslinking assignment will start from
    'crosslinking_start_shard': 'int16',
    # Records about the most recent crosslink `for each shard
    'crosslink_records': [CrosslinkRecord],
    # Total balance of deposits
    'total_deposits': 'int256',
    # Used to select the committees for each shard
    'dynasty_seed': 'hash32',
    # Last time the crosslink seed was reset
    'dynasty_seed_last_reset': 'int64'
}
```

```ShardAndCommittee``` 对象的形式：

```
fields = {
    # The shard ID
    'shard_id': 'int16',
    # Validator indices
    'committee': ['int24']
}
```

每一个```ValidatorRecord```都包含了一个验证者（validator）的信息：
```
fields = {
    # The validator's public key
    'pubkey': 'int256',
    # What shard the validator's balance will be sent to
    # after withdrawal
    'withdrawal_shard': 'int16',
    # And what address
    'withdrawal_address': 'address',
    # The validator's current RANDAO beacon commitment
    'randao_commitment': 'hash32',
    # Current balance
    'balance': 'int64',
    # Dynasty where the validator  is inducted
    'start_dynasty': 'int64',
    # Dynasty where the validator leaves
    'end_dynasty': 'int64'
}
```  

```CrosslinkRecord``` 包含了上一个被提交到链上的完整的交联信息：
```
fields = {
    # What dynasty the crosslink was submitted in
    'dynasty': 'int64',
    # The block hash
    'hash': 'hash32'
}
```

##### 信标链处理
（未翻译完成，预计2018.8.31日完成）
