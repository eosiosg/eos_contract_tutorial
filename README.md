# EOS contract develop tutorial

## setup environment

1. 下载IDE EOS studio https://www.eosstudio.io/


## basic concepts
1. 账号概念及合约概念
    1. 账号代表了用户  
>每一个账号都有自己的权限管理系统, 默认权限等级为 owner 和 active

    2. 合约是部署在账号下的一段代码  
>同一段代码可以重复部署在不同账号下, 例如可以把eosio.token的代码部署在某用户账号下, 这不会对现有的系统token合约产生任何影响

    3. 合约调用
          a. ACTION
>合约调用的最小单元是ACTION, 每个ACTION由account, action, data 组成(可以执行cleos push action查看命令行提示). 这里ACTION是概念上的, 大写用以跟action区分
>- account 指的是contract, 也就是这个contract是部署在哪个账号下的
>- action 是contract的接口, 也就是可被外部调用的方法
>- data 是action执行所需要的数据

          b. TRANSACTION
>- 一个或多个ACTION可以组成TRANSACTION  
>- 如果TRANSACTION执行成功, 里面的所有ACTION一定全部执行成功, 如果任何一个ACTION失败, 则TRANSACTION执行失败, 并且里面所有的ACTION全部执行失败  
>- TRANSACTION要求提交的用户签名, 该签名对应account下的权限等级(permission level) acction@permission, 例如 accountaaaaa@active

          c. 调用关系
> 用transfer举例 Alice transfer 10 EOS to Bob
>- account是eosio.token 系统的token合约
>- action是transfer
>- data是 sender, recipient, amount, memo, 代表从 Alice 转到 Bob, 转移的数量是 10.0000 EOS, 备注是memo的内容
>- 这个ACTION会被封装在一个TRANSACTION里, 需要sender的签名,也就是Alice的签名
>- 本质上的执行逻辑是: 1. Alice在eosio.token中记录的balance金额减少 10.0000 EOS 2. Bob在eosio.token中记录的balance金额增加10.0000 EOS


2. 系统账号/合约
    1. eosio 系统合约,包括投票系统, 资源系统, 多签系统, 出块规则等
    1. eosio.token 代币合约


3. 资源概念
    1. 资源包括 CPU, NET, RAM
    1. CPU 和 NET 是执行TRANSACTION的时候的系统消耗
    1. RAM 是合约记录数据所占据的资源, 部署合约也需要消耗RAM, 执行TRANSACTION时可能会触发消耗额外的RAM(记录数据),RAM的付费可以由合约账号支付,也可以由发起交易的用户支付,取决于合约实现


4. 合约执行逻辑
>1. BP解析交易数据
>2. 检查交易是属于合约级别还是系统级别 (以下是合约级别, 如果是系统级别是一个不同的逻辑)
>3. 加载执行合约所需要的context
>4. 在wasm引擎中执行交易, 目前为wabt
>     1. 记录cpu/net消耗
>     2. 记录restore状态标记
>     3. 校验交易签名合法性
>     4. 执行交易, 可能修改相关数据表内容
>     5. 如果成功, 则继续, 如果失败则回滚至合法的restore标记点


5. table的概念
  >- 1. table由multi-index实现
  >- 2. cleos get table 时需要提供 account, scope, table
  >      - account是合约账号, 类比 mysql 的database
  >      - scope是table数据存放的地址, 可以是自定义内容的, 类比 mysql 的schema
  >      - table是存放数据的地方, 名称可以自定义, 类比 mysql 的table


## Appendix A: references
1. smart contract IDE
  - EOS studio https://www.eosstudio.io/
1. EOS explorer
  - https://bloks.io (recommend)
  - https://www.eosx.io
  - https://eosq.app
