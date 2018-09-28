编辑人：@GirpZhang 审阅人：@rectinajh
### 引言
`在互联网十分发达的今天，很多人已经习惯了数字支付，只要有网，随时都可以进行转账操作；但是依旧有许多情况下没有网络连接，比如一些偏远地区、岛屿、地下室等网络信号难以覆盖的地方或者手机停机等突发状况，这时候如何进行支付呢？有人可能会说支付宝微信即使没有网络也可以出示付款码，但这种方式的前提是对方(收款方)必须有网，我们在前面列举的一些场景中是交易双方都没有网络连接的，这就需要某些新技术和解决方案了，SmartRaiden 无网微支付就很好地解决了这一问题，下面我们就从设计和实现角度来看看它到底是什么。`
### 介绍
SmartRaiden 无网微支付是基于 Raiden 网络扩展的 SmartMesh 支付协议，SmartMesh
用户之间通过手机 WiFi 或其他直连信号，P2P 传输签名加密交易信息，进行无网支付。SmartRaiden 是使用 Golang 编写的标准的 Raiden 网络协议，它可以在移动平台上实现离线交易，并且可以在没有互联网连接时运行。
SmartRaiden 核心实现代码见：<https://github.com/SmartMeshFoundation/SmartRaiden/tree/master/network/rpc/contracts>
### Raiden Network
Raiden Network 是一种 off-chain 的可扩展性解决方案，可在 Ethereal 上实现符合 erc-20 标准的 token 传输。它允许参与者之间在没有任何全局共识机制的情况下进行安全的 token 交易，这是通过预先设置的带有数字签名和锁哈希的链上存款支付来实现的。因为仍要依赖于几个链上的操作来打开和关闭一对节点的支付通道，所以网络上的每对节点都难以创建通道。但是，如果（至少）存在一个通道，通过网络中的其他节点来连接两个支付节点，那么我们就不需要为这两个节点创建单独的通道了。该网络被命名为 Raiden Network，它具有类似路由算法和互锁信道通信的所有合约。
Smartraiden 使用与 raiden 网络相同原理的支付通道网络技术，但 Smartraiden 是为更好地适应移动节点而特别设计的。我们将包含移动节点的支付渠道网络称为 smartraiden 网络，以区别于传统的 raiden 网络。
![支付通道网络图](https://upload-images.jianshu.io/upload_images/7286323-27d944d1f9a345d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### SmartRaiden 交易展示
假设我们有一个节点使用 AET 令牌连接到我们的通道网络，在这种情况下，该节点连接到另外 5 个节点，可以轻易将 token 转移到其直连节点。如果这个通道网络变得复杂，那么我们通过几个节点转移 token，这个通道的节点状态会连续改变。
交易前：
![](https://upload-images.jianshu.io/upload_images/7286323-731953118266ce20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

交易后：
![](https://upload-images.jianshu.io/upload_images/7286323-6bcd94be33c129f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在该图中，五个节点的地址分别是：
* node 1 : 0x69C5621db8093ee9a26cc2e253f929316E6E5b92
* node 2 : 0x31DdaC67e610c22d19E887fB1937BEE3079B56Cd
* node 3 : 0xf0f6E53d6bbB9Debf35Da6531eC9f1141cd549d5
* node 4 : 0x6B9E4D89EE3828e7a477eA9AA7B62810260e27E9
* node 5 : 0x088da4d932A716946B3542A10a7E84edc98F72d8

交易流程从节点 1 开始，到节点 5 结束。然而，在图中我们只有一条到 5 的路径，即 1 到 2 到 4 到 5。交易完成后，此通道中 token 余额的变更为：
node 1 to node 2 : 0xc4327c664D9c47230Be07436980Ea633cA3265e4
node 1 initial deposit : 200
node 2 initial deposit : 100
node 1 balance : 150 
node 2 balance : 150
node 2 to node 3 : 0xd1102D7a78B6f92De1ed3C7a182788DA3a630DDA
node 2 initial deposit : 100
node 3 initial deposit : 100
node 2 balance : 100 
node 3 balance : 100
node 2 to node 4 : 0xdF474bBc5802bFadc4A25cf46ad9a06589D5AF7D
node 2 initial deposit : 200
node 4 initial deposit : 100 
node 2 balance : 150 
node 4 balance : 150
node 4 to node 5 : 0xd5CF2248292e75531d314B118a0390132bc7a5F0
node 4 initial deposit : 100
node 5 initial deposit : 100
node 4 balance : 50
node 5 balance : 150

### SmartRaiden 合约和通道的各种状态
SmartRaiden 合约包括：
- Netting Channel Library : 0xad5cb8fa8813f3106f3ab216176b6457ab08eb75
- Channel Manager Library : 0xdb3a4dbae2b761ed2751f867ce197c531911382a
- Registry Contract : 0x68e1b6ed7d2670e2211a585d68acfa8b60ccb828
- Discovery Contract : 0x1e3941d8c05fffa7466216480209240cc26ea577

Spectrum 合约发布地址 = 0x41Df0be8c4e4917f9Fc5F6F5F32e03F226E2410B

#### 通道各状态解读
这里结合 SmartRaiden 合约代码来解读通道在生命周期内的各个状态。
*   通道不存在

通道不存在有两种情况：一种是我们的通道永远不存在，另一种是我们已经完成了交易，因此通道和参与者的所有数据都已被删除。在这两种情况下，我们无法验证节点之间的交易，除非为交易再创建通道。

*   通道打开

在节点与其直连节点之间创建通道时，通道的创建者有权指定 token 的地址，对方的地址，要存入的 token 的数量以及结算的时间段。一旦通道打开，参与者就可以进行交易。合约实现如下：
```
  /*
        允许任何人调用,多次调用.
        创建通道:
        1. 允许任意两个不同有效地址之间创建通道
        2. 两地址之间不能有多个通道
        参数数说明:
        participant1,participant2 通道参与双方,都必须是有效地址,且不能相同
        settle_timeout 通道结算等待时间
    */
    /// @notice function to open a payment channel.
    /// @dev It can be invoked by anyone, any times. Any pair of distinct addresses can create a channel, but cannot create multiple channels within the pair.
    /// @param participant1     An address for a channel participant
    /// @param participant2     The address for another other channel participant, cannot be the same as participant1.
    /// @param settle_timeout   Waited time between channel close and channel settle.
    function openChannel(address participant1, address participant2, uint64 settle_timeout)
    settleTimeoutValid(settle_timeout)
    public
    {
        bytes32 channel_identifier;
        require(participant1 != 0x0);
        require(participant2 != 0x0);
        require(participant1 != participant2);
        channel_identifier = getChannelIdentifier(participant1, participant2);
        Channel storage channel = channels[channel_identifier];

        // ensure that channel has not been created.
        require(channel.state == 0);
        // Store channel information
        channel.settle_timeout = settle_timeout;
        channel.open_block_number = uint64(block.number);
        // Mark channel as opened
        channel.state = 1;

        emit ChannelOpened(channel_identifier, participant1, participant2, settle_timeout);
    }

```
*   通道存款

在支付通道打开后，只有一个节点进行存款，因此只有这个节点可以将他的 token 转移给他的交易对象。然后该节点可以发送消息，通知已存在为对方打开的交易支付渠道，之后对方也能够存入其 token。
存款部分实现是这样：
```
 /*
        必须在通道 open 状态调用,可以重复调用多次,任何人都可以调用.
        参数说明:
        participant 存钱给谁
        partner 通道另一方
        amount 存多少 token
    */
    /// @notice internal function to be invoked when depositing tokens into this channel.
    /// @dev    this function must be invoked when channel has opened yet.
    /// @param participant      channel creator
    /// @param partner          the counterpart corresponding to participant.
    /// @param amount           the amount of tokens deposited in this channel.
    /// @param from             another address that transfers tokens to this channel.
    /// @param need_transfer    a boolean value confirms whether this channel need another source of value.
    function depositInternal(address participant, address partner, uint256 amount, address from, bool need_transfer)
    internal
    {
        /*
        为0,可能会在 TransferFrom 的时候成功,但是没有任何意义.

        */
        require(amount > 0);
        uint256 total_deposit;
        bytes32 channel_identifier;
        channel_identifier = getChannelIdentifier(participant, partner);
        Channel storage channel = channels[channel_identifier];
        Participant storage participant_state = channel.participants[participant];
        total_deposit = participant_state.deposit;
        if (need_transfer) {
            // Do the transfer
            require(token.transferFrom(from, address(this), amount));
        }
        require(channel.state == 1);

        // Update the participant's channel deposit
        total_deposit += amount;
        participant_state.deposit = total_deposit;

        emit ChannelNewDeposit(channel_identifier, participant, total_deposit);
    }

```
一般将通道打开和存款放在一起，这样可以节约不少 gas：
```
 /*
        有三种调用途径:
        分别是
        1. 用户直接调用openChannelWithDeposit,
        2. token 是 ERC223,通过 tokenFallback 调用
        3. token 提供了 ApproveAndCall, 通过receiveApproval调用
    */
    /// @notice function to open channel and meanwhile make some deposits inside with certain threshold of settle_timeout.
    /// @dev    parameter settle_timeout has to meet certain threshold in which case this function is able to operate.
    /// @param participant      channel creator
    /// @param partner          the counterpart corresponding to participant.
    /// @param settle_timeout   time period for channel to settle.
    /// @param amount           the amount of tokens to be deposited into this channel.
    /// @param from             another third party address to deposit tokens if need_transfer is true.
    /// @param need_transfer    a boolean value to confirm whether this channel need any token from outside.
    function openChannelWithDepositInternal(address participant, address partner, uint64 settle_timeout, uint256 amount, address from, bool need_transfer)
    settleTimeoutValid(settle_timeout)
    internal
    {
        bytes32 channel_identifier;
        require(participant != 0x0);
        require(partner != 0x0);
        require(participant != partner);
        require(amount > 0);
        channel_identifier = getChannelIdentifier(participant, partner);
        Channel storage channel = channels[channel_identifier];
        Participant storage participant_state = channel.participants[participant];

        // make sure that this channel has not been created.
        require(channel.state == 0);

        // Store channel information
        channel.settle_timeout = settle_timeout;
        channel.open_block_number = uint64(block.number);

        // Mark channel as opened
        channel.state = 1;
        if (need_transfer) {
            require(token.transferFrom(from, address(this), amount));
        }
        participant_state.deposit = amount;
        emit ChannelOpenedAndDeposit(channel_identifier, participant, partner, settle_timeout, amount);
    }
```

*   通道转账

一旦节点通过 AET 令牌连接到支付通网络，它就可以访问另外 5 个节点。这个节点很容易将其 token 发送到另一个直连的节点，但如果想要发送到它们之间的中间节点，它们都需要构建到中间节点的通道，如果这些节点中的 token 可以满足此交易，则发生交易。
交易部分实现：
```
/*
       功能:在不关闭通道的情况下提现,任何人都可以调用

       一旦一方提出 withdraw, 实际上和提出 cooperative settle 效果是一样的,就是不能再进行任何交易了.
       必须等待 withdraw 完成才能重置交易数据,重新开始交易
       参数说明:
       participant,partner 通道参与双方
       participant_balance: 取款方的 balance 是多少
       participant_withdraw:取款方需要取多少钱
       participant_signature,partner_signature 双方对这次提现的签名
    */
    /// @notice function to withdraw tokens while channel state is open. Anyone can invoke it.
    /// @dev Once a participant proposes to withdraw, which has the same effect as cooperative settle, that is, any transfer are forbidden.
    /// @dev After withdraw completes, transfers are able to resume.
    /// @param participant              The address for a channel participant
    /// @param partner                  The address for the counterparts of participate
    /// @param participant_balance      The token balance of participant
    /// @param participant_withdraw     The amount of tokens that participant needs to withdraw
    /// @param participant_signature    The signature of participant
    /// @param partner_signature        The signature of partner
    function withDraw(
        address participant,
        address partner,
        uint256 participant_balance,
        uint256 participant_withdraw,
        bytes participant_signature,
        bytes partner_signature
    )
    public
    {
        uint256 total_deposit;
        bytes32 channel_identifier;
        uint64 open_block_number;
        uint256 partner_balance;
        channel_identifier = getChannelIdentifier(participant, partner);
        Channel storage channel = channels[channel_identifier];
        open_block_number = channel.open_block_number;
        require(channel.state == 1);
        // 验证双方签名有效
        require(participant == recoverAddressFromWithdrawProof(channel_identifier,
            participant,
            participant_balance,
            participant_withdraw,
            open_block_number,
            participant_signature
        ));
        require(partner == recoverAddressFromWithdrawProof(channel_identifier,
            participant,
            participant_balance,
            participant_withdraw,
            open_block_number,
            partner_signature
        ));

        Participant storage participant_state = channel.participants[participant];
        Participant storage partner_state = channel.participants[partner];
        //The sum of the provided deposit must be equal to the total available deposit
        total_deposit = participant_state.deposit + partner_state.deposit;
        partner_balance = total_deposit - participant_balance;


        /*
            谨慎一点,应该先扣钱再转账,尽量按照规范来,如果有的话.
        */

        /*
            提议提现的人,金额一定不能是0,否则就应该调用 cooperative settle
        */
        require(participant_withdraw > 0);
        require(participant_withdraw <= participant_balance);
        //防止溢出
        require(total_deposit >= participant_balance);
        require(total_deposit >= partner_balance);

        participant_balance -= participant_withdraw;
        participant_state.deposit = participant_balance;
        partner_state.deposit = partner_balance;

        // 相当于 通道 settle 又新开了.老的签名都作废了.
        channel.open_block_number = uint64(block.number);
        require(token.transfer(participant, participant_withdraw));


        //channel's status right now
        emit ChannelWithdraw(channel_identifier, participant, participant_balance, partner, partner_balance);

    }
```

*   通道关闭

如果任何节点想要关闭连接到它的某个通道，他可以调用 close 方法。之后，通道关闭操作者及其对应节点需要在结算期间提交余额证明。
关闭通道操作：
```
/*
        只能是通道参与方调用,只能调用一次,必须是在通道打开状态调用.
        参数说明:
        partner 通道的另一方
        transferred_amount 另一方给的直接转账金额
        locksroot 另一方彻底完成交易集合
        nonce 另一方交易编号
        additional_hash 为了辅助实现用
        signature partner 的签名
    */
    /// @notice function to close a payment channel with balance proof from his channel counterpart.
    /// @dev It can be invoked merely when channel state is open, and only by channel participants and only once.
    /// @param partner              The address of channel partner.
    /// @param transferred_amount   The amount of tokens that partner has been transferred till now.
    /// @param locksroot            The set of incomplete transfers that have been hash locked in partner's balanceproof.
    /// @param nonce                The newest serial number for partner's transfer.
    /// @param additional_hash      A hash value for auxiliary usage.
    /// @param signature            Partner's signature.
    function closeChannel(
        address partner,
        uint256 transferred_amount,
        bytes32 locksroot,
        uint64 nonce,
        bytes32 additional_hash,
        bytes signature
    )
    public
    {
        bytes32 channel_identifier;
        address recovered_partner_address;
        channel_identifier = getChannelIdentifier(msg.sender, partner);
        Channel storage channel = channels[channel_identifier];
        require(channel.state == 1);
        // Mark the channel as closed and mark the closing participant
        channel.state = 2;
        // This is the block number at which the channel can be settled.
        channel.settle_block_number = channel.settle_timeout + uint64(block.number);
        // Nonce 0 means that the closer never received a transfer, therefore never received a
        // balance proof, or he is intentionally not providing the latest transfer, in which case
        // the closing party is going to lose the tokens that were transferred to him.
        if (nonce > 0) {
            Participant storage partner_state = channel.participants[partner];
            recovered_partner_address = recoverAddressFromBalanceProof(
                channel_identifier,
                transferred_amount,
                locksroot,
                nonce,
                channel.open_block_number,
                additional_hash,
                signature
            );
            require(partner == recovered_partner_address);
            partner_state.balance_hash = calceBalanceHash(transferred_amount, locksroot);
            partner_state.nonce = nonce;
        }
        emit ChannelClosed(channel_identifier, msg.sender, locksroot, transferred_amount);
    }
```

*   通道结算

一旦调用 close 方法， timeout 开始计时。在此期间，两个节点都会提交最新消息。 timeout 结束后，通道完成结算。合约代码：
```
 /*
        只能通道参与方调用,不限制 close 和非 close 方,可以调用多次,只要在有效期内.
        包括 closing 方和非 close 方都可以反复调用在,只要能够提供更新的 nonce 即可.
        目的是更新partner 的 balance proof, 只是自己直接调用,不经过第三方委托.
        参数说明:
        partner: 证据待更新一方
        transferred_amount locksroot 的直接转账金额
        locksroot partner 未彻底完成交易集合
        nonce partner 给出交易变化
        additional_hash 实现辅助信息
        partner_signature partner 一方对于给出证据的签名
   */
    /// @notice function to update channel partner's balance proof.
    /// @dev It can be invoked merely by channel participants with multiples times if in channel lifecycle.
    /// @param partner              The address whose balance proof is about to get updated.
    /// @param transferred_amount   The amount of tokens that has been transferred from partner.
    /// @param locksroot            The set of transfers that has been hash locked.
    /// @param nonce                The serial number of transfers that partner has sent out.
    /// @param additional_hash      The hash value used for auxiliary usage.
    /// @param partner_signature    The signature of channel partner.
    function updateBalanceProof(
        address partner,
        uint256 transferred_amount,
        bytes32 locksroot,
        uint64 nonce,
        bytes32 additional_hash,
        bytes partner_signature
    )
    public
    {
        bytes32 channel_identifier;
        channel_identifier = getChannelIdentifier(partner, msg.sender);
        Channel storage channel = channels[channel_identifier];
        Participant storage partner_state = channel.participants[partner];
        require(channel.state == 2);
        require(channel.settle_block_number >= block.number);
        //明确要求,必须有更新的 balance proof, 否则没必要调用
        require(nonce > partner_state.nonce);

        require(partner == recoverAddressFromBalanceProof(
            channel_identifier,
            transferred_amount,
            locksroot,
            nonce,
            channel.open_block_number,
            additional_hash,
            partner_signature
        ));
        partner_state.balance_hash = calceBalanceHash(transferred_amount, locksroot);
        partner_state.nonce = nonce;
        emit BalanceProofUpdated(channel_identifier, partner, locksroot, transferred_amount);
    }
```

### SmartRaiden 的主要功能和特性
SmartRaiden 的主要目标是构建一个结构，为 SmartRaiden Network 实施一个 off chain 可扩展性解决方案，从而提高可用性，兼容性和安全性。
其常规功能包括查询，注册，通道依赖和不同场景下的传输，API 详见 [rest_api](https://smartraiden.readthedocs.io/en/latest/rest_api/)。
其他功能包括：
* 跨平台和移动端适应
SmartRaiden 网络支持多个平台，并且可以实现移动智能设备上的分布式微支付。SmartRaiden 目前可以在 Windows，Linux，Android，iOS 等上运行。SmartRaiden 在 XMPP 而非 P2P上构建自己的消息传递机制，同时具备单独的节点和启动过程，确保它能够在具有正确操作的多个平台上运行。
* 节点状态同步
为了保证交易安全，SmartRaiden 采用状态机来设计节点，确保相关操作是原子性的。例如，它能确保接收到的数据解锁记录和ACK报文发送的信息一致，两者都成功或者都不成功，不存在中间状态。在交易处理过程中，如果出现任何错误的情况，都会确保双方的交易状态一致，在崩溃恢复后，要么交易继续，要么交易失败，没有任何 token 损失。

* 无网付款
它是 SmartRaiden 中添加的一项特殊功能。通过 meshbox 中的网络构建功能，SmartRaiden 能够在不依赖互联网的情况下实施 off chain 的资金转移。

* 第三方代委托
第三方委托服务，也称为 SmartRaiden 监控，主要用于在移动设备在离线时由第三方委托来帮助在区块链上强制执行 UpdateTransferDelegate 或 WithDraw 操作。第三方服务与其系统外部的 App，SmartRaiden 和 spectrum 三部分进行交互。

* 固定费用
与 Lightning Network 类似，我们在 token 转账的过程中还有一个额外的固定费率收费功能。受此费用的激励，该路由上的所有节点将保留通道的余额，以提高效率和交易的成功率。

## 结论

前面结合代码实现解读了有关 SmartRaiden 的概念和功能。如需进一步使用，请参阅安装[说明](https://smartraiden.readthedocs.io/en/latest/installation_guide/)和[教程](https://smartraiden.readthedocs.io/en/latest/api_walkthrough/)或 [SmartRaiden 规范](https://smartraiden.readthedocs.io/en/latest/spec/)