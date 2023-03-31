# Archway
    据公开资料显示，Archway在种子轮融资中筹集了2100万美元，由Hashed和CoinFund共同领投。
    由于Archway生态还处于非常早期，想成为项目早期用户，除了发布Dapp外，可以部署节点，为生态做贡献。
    
    目前存在三个测试网：
    1、社区测试网Torii：社区维护的测试网，用于完成激励测试网挑战。目前没有激励性挑战，Torii可能会在未来被弃用。核心贡献者将不再为 Torii 网络运行验证器。
    2、实验测试网Titus：面向核心 SDK 开发人员的实验性测试网络，用于测试 Archway 网络中的最新功能。
    3、当前测试网Constantine：为构建 dApp 的 dapp 开发人员提供稳定的测试网络。
    
    目前主要是参与constantine-1测试网，为后期领取用户奖励做好准备。

## 运行节点
### 先决条件
#### 硬件要求
    CPU：x86_64 processor
    内存：16 GB
    磁盘：500 GB to 2 TB 
    系统：Ubuntu 20.04.5

#### 软件要求
##### 更新依赖包
    sudo apt-get update
    sudo apt-get install build-essential docker-ce docker-ce-cli containerd.io docker-compose-plugin git
    
##### 安装Go
    cd $HOME
    wget "https://go.dev/dl/go1.19.4.linux-amd64.tar.gz"
    sudo rm -rf /usr/local/go
    sudo tar -C /usr/local -zxvf go1.19.4.linux-amd64.tar.gz

    echo "export GOROOT=/usr/local/go" |  sudo tee -a /etc/profile
    echo "export GOPATH=$HOME/go" |  sudo tee -a /etc/profile
    echo "export PATH=$PATH:/usr/local/go/bin:$GOPATH/bin" |  sudo tee -a /etc/profile
    echo "export GO111MODULE=on" |  sudo tee -a /etc/profile
    echo "export GOPROXY=https://goproxy.cn" |  sudo tee -a /etc/profile
    source /etc/profile
    go version

### 节点安装
#### 源码构建
    git clone https://github.com/archway-network/archway.git
    cd archway
    git fetch
    git checkout v0.2.0
    make install
    
    archwayd version --long | grep -e version -e commit
<img width="503" alt="微信截图_20230329154759" src="https://user-images.githubusercontent.com/100336530/228712247-f0372746-61fe-49c0-916f-15a3d8186519.png">

#### 初始化节点
    MONIKER=your-moniker-name
    archwayd init $MONIKER --chain-id constantine-1
    
    archwayd config chain-id constantine-1
    archwayd config keyring-backend os
<img width="859" alt="微信截图_20230329155039" src="https://user-images.githubusercontent.com/100336530/228712637-fe696067-712b-4696-bf95-c75c2ada725b.png">

### 创建账号
    WALLET=your-key-name
    archwayd keys add $WALLET
<img width="475" alt="微信截图_20230329155816" src="https://user-images.githubusercontent.com/100336530/228712950-5728d10e-aef2-48df-a107-9fec6bb3affa.png">

### 节点配置
#### 更改端口
    NODES_NUM=“5”
    sed -i.bak -e "\ 
    s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:$((NODES_NUM+26))658\"%; \ 
    s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://0.0.0.0:$((NODES_NUM+26))657\"%; \ 
    s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:$((NODES_NUM+6))060\"%; \ 
    s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:$((NODES_NUM+26))656\"%; \ 
    s%^external_address = \"\"%external_address = \"`echo $(wget -qO- eth0.me):$((NODES_NUM+26))656`\"%; \ 
    s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":$((NODES_NUM+26))660\"%" $HOME/.archway/config/config.toml

    sed -i.bak -e "\ 
    s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:$((NODES_NUM+1))317\" %; \ 
    s%^address = \":8080\"%address = \":$((NODES_NUM+8))080\"%; \ 
    s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:$((NODES_NUM+9))090\"%; \ 
    s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:$((NODES_NUM+9))091 \"% " $HOME /.archway/config/app.toml
    
    echo "export NODE=http://localhost:$((NODES_NUM+26))657" >> $HOME/.bash_profile && \
    source $HOME/.bash_profile && \
    archwayd config node $NODE

#### 内存优化
    indexer="null" && \
    min_retain_blocks=1 && \
    snapshot_interval="100" && \
    pruning="custom" && \
    pruning_keep_recent="100" && \
    pruning_keep_every="0" && \
    pruning_interval="10" && \
    min_retain_blocks="1" && \
    inter_block_cache="false"

    sed -i.bak -e "s/^indexer *=.*/indexer = \"$indexer\"/"  $HOME/.archway/config/config.toml && \
    sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/"  $HOME/.archway/config/app.toml && \
    sed -i.bak -e "s/^snapshot -interval *=.*/snapshot-interval = \"$snapshot_interval\"/"  $HOME/.archway/config/app.toml && \
    sed -i.bak -e "s/^pruning *=.*/pruning = \"$pruning\"/"  $HOME/.archway/config/app.toml && \
    sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/"  $HOME/.archway/config/app.toml && \
    sed -i.bak -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/"  $HOME/.archway/config/app.toml && \
    sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/"  $HOME/.archway/config/app.toml && \
    sed -i.bak -e "s/^min-retain-blocks *=.*/min-retain-blocks = \"$min_retain_blocks\"/"  $HOME/.archway/config/app.toml && \
    sed -i.bak -e "s/^inter-block-cache *=.*/inter-block-cache = \"$inter_block_cache\"/"  $HOME/.archway/config/app.toml

#### 配置种子节点与对等对节
    SEEDS="5c10d3d84adb970474eff3c9b5d8fe50fd2dbbfb@144.76.18.199:26656,802993601906fae95a19e96f2e8bd538b0d209d5@35.222.155.3:26656,1570fd9b344af3bf77ec7eefffe485033f412080@65.109.112.178:26656,a2ad516c5301fb1a9793b0c9bd2195e16721ed73@34.170.18.34:26656"

    sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$SEEDS\"/; s/^seeds *=.*/seeds = \"$SEEDS\"/" $HOME/.archway/config/config.toml

### 加入网络
##### 获得创世文化
    curl -s https://rpc.constantine-1.archway.tech/genesis |jq -r .result.genesis > ${HOME}/.archway/config/genesis.json
    
##### 启动节点
    目前需要标志跳过187340才能正确同步节点：
    archwayd start --x-crisis-skip-assert-invariants --unsafe-skip-upgrades 187340
    
    或者直接从该特定块同步
    archwayd start --x-crisis-skip-assert-invariants --halt-height 187341

##### 检查同步情况
    archwayd status 2>&1 | jq .SyncInfo
<img width="534" alt="微信截图_20230329165245" src="https://user-images.githubusercontent.com/100336530/228716420-ee7934ef-52fd-4cdf-88d1-180e2eecca0d.png">

## 成为验证者
### 获得测试币
    加入官方DC：https://discord.gg/archwayhq
    进入faucet频道
    输入接水命令：!faucet <wallet-address>
    每天可领取一次，每次获得1个CONST
<img width="806" alt="微信截图_20230331110126" src="https://user-images.githubusercontent.com/100336530/229012149-19e3fb76-4100-48e9-bc31-5824359aa4f4.png">
    
### 相关检查
    节点需完成同步才能操作，如下命令返回FALSE，则节点已同步
    curl -s $NODE/status | jq .result.sync_info.catching_up
    
    创建验证器最小数量1000000，检查钱包余额
    ADDRESS=<your-wallet-address>
    archwayd q bank balances $ADDRESS

### 创建验证器
    archwayd tx staking create-validator \
    --amount=1000000uconst \
    --pubkey=$(archwayd tendermint show-validator) \
    --moniker=$MONIKER \
    --chain-id=constantine-1 \
    --commission-rate="0.01" \
    --commission-max-rate="0.10" \
    --commission-max-change-rate="0.01" \
    --min-self-delegation=1 \
    --fees=5000uconst \
    --from=$WALLET
<img width="857" alt="微信截图_20230330112130" src="https://user-images.githubusercontent.com/100336530/228720322-795bf2f0-4ee0-4047-a272-f23da3ec586f.png">

## 常用命令
##### 解除监禁
    archwayd tx slashing unjail --chain-id=constantine-1 --from=<your-wallet-address> --gas auto --fees 3000uconst
    
    E.G：
    archwayd tx slashing unjail --chain-id=constantine-1 --from=archway1a9u2jkg5ajfqax2szy3w4axne369mwlmt3y92a --gas auto --fees 3000uconst

##### 修改打开的文件数
    Linux 每个进程可以打开的默认文件数为1024，打开的次数超过这个数量，就会导致进程崩溃
    ulimit -n 4096

##### 检查共识
    NODE=http://localhost:26657
    curl -s $NODE/consensus_state  | jq '.result.round_state.height_vote_set[0].prevotes_bit_array'

##### 链接PEERS
    curl -s $NODE/net_info | jq -r '.result.peers[] | "\(.node_info.id)@\(.remote_ip):\(.node_info.listen_addr | split(":")[2])"' | wc -l

##### JAIL，TOMBSTONED，START_HEIGHT，INDEX_OFFSET
    archwayd q slashing signing-info $(archwayd tendermint show-validator)

##### 投票
    archwayd tx gov vote <PROPOSAL_ID> <yes|no> --from $WALLET --fees 200uconst -y

##### 检查所有投票的提案
    archwayd q gov proposals --voter <ValidatorAddress>
    
##### 编辑验证器
    archwayd tx staking edit-validator --website="<YOUR_WEBSITE>" --details="<YOUR_DESCRIPTION>" --moniker="<YOUR_NEW_MONIKER>" --from =$WALLET --fees 5000uconst

##### 增加担保
    archwayd tx staking delegate <ValidatorAddress> <TOKENS_COUNT>uconst--from $WALLET --fees 5000uconst -y

##### 取消委托
    archwayd tx staking unbond <ValidatorAddress> <TOKENS_COUNT>uconst --from $WALLET --fees 5000uconst -y

##### 发送代币
    archwayd tx bank send $WALLET <WALLET_TO> <TOKENS_COUNT>uconst --fees 5000uconst --gas auto

##### 所有活跃验证者列表
    archwayd q staking validators -o json --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '.tokens + " - " + .description.moniker' | sort -gr | nl

##### 所有不活跃的验证者列表
    archwayd q staking validators -o json --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '.tokens + " - " + .description.moniker' | sort -gr | nl





































