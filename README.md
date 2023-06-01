# Archway
    据公开资料显示，Archway在种子轮融资中筹集了2100万美元，由Hashed和CoinFund共同领投。
    由于Archway生态还处于非常早期，想成为项目早期用户，除了发布Dapp外，可以部署节点，为生态做贡献。
    
    目前存在三个测试网：
    1、社区测试网Torii：社区维护的测试网，用于完成激励测试网挑战。目前没有激励性挑战，Torii可能会在未来被弃用。核心贡献者将不再为 Torii 网络运行验证器。
    2、实验测试网Titus：面向核心 SDK 开发人员的实验性测试网络，用于测试 Archway 网络中的最新功能。
    3、当前测试网Constantine：为构建 dApp 的 dapp 开发人员提供稳定的测试网络。
    
    目前主要是参与constantine-3测试网，为后期领取用户奖励做好准备。

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
    git checkout v0.5.2
    make install
    
    archwayd version --long | grep -e version -e commit
<img width="503" alt="微信截图_20230329154759" src="https://user-images.githubusercontent.com/100336530/228712247-f0372746-61fe-49c0-916f-15a3d8186519.png">

#### 初始化节点
    MONIKER=your-moniker-name
    archwayd init $MONIKER --chain-id constantine-3
    
    archwayd config chain-id constantine-3
    archwayd config keyring-backend os
<img width="859" alt="微信截图_20230329155039" src="https://user-images.githubusercontent.com/100336530/228712637-fe696067-712b-4696-bf95-c75c2ada725b.png">

    sed -i 's|^pruning *=.*|pruning = "custom"|g' $HOME/.archway/config/app.toml
    sed -i 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|g' $HOME/.archway/config/app.toml
    sed -i 's|^pruning-interval *=.*|pruning-interval = "10"|g' $HOME/.archway/config/app.toml
    sed -i 's|^snapshot-interval *=.*|snapshot-interval = 0|g' $HOME/.archway/config/app.toml
    sed -i 's|^minimum-gas-prices *=.*|minimum-gas-prices = "0.0001aconst"|g' $HOME/.archway/config/app.toml

### 创建账号
    WALLET=your-key-name
    archwayd keys add $WALLET
<img width="475" alt="微信截图_20230329155816" src="https://user-images.githubusercontent.com/100336530/228712950-5728d10e-aef2-48df-a107-9fec6bb3affa.png">

### 节点配置
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
    SEEDS="3c5bc400c786d8e57ae2b85639273d1aec79829a@34.31.130.235:26656"
    PEERS="900950a031cb758b761198e52b07fcc17616bd76@archway-testnet.nodejumper.io:40656,6b137e1df61936010ea30a354d8abd7010598e29@35.239.130.141:26656,3591dd903e95c9b25618f90c4a6bda63861ab8ec@65.109.92.79:45656,f7a7c6bf673c201c55ecf0d249df43826293d9d4@176.9.28.41:26656,874f0042c20d3808eccb86b523fffe42903034b8@95.217.144.107:11556,b6031525e988eefd03452807806f08b0e9bc3289@15.235.46.50:26656,e5e71ccd387eba74fec51b211e9236fca965af40@46.4.5.45:11556,a77c935197691d0d526fcb65a08df364a96bea9b@142.132.134.112:27656,d87eada9b5d98e68850bca886fc0cbb8ee36209e@65.108.75.174:32656,2854e7247155c5c0c418de40ed168850b4c73c60@85.232.252.19:26156,5c2a752c9b1952dbed075c56c600c3a79b58c395@195.3.220.140:26946,792651a926b8407dfa54d9e94c7cd09e1b92ea92@65.109.92.148:14656,d1334258b592ebccb85a917aa65976b74e254a60@65.109.65.248:31656,857515ed6ab05e8e59f74e1050cb9e653e899cac@159.223.220.1:26656,e8d60ff778f3c27f54382ff22c7ac071f2a81027@35.223.36.227:26656,35cff40de4efbb164d19e39f335d28bf7a301cff@65.109.89.18:44656,c0d0c9f1ef645bcf1c214b05581c9d4a4b45e97e@185.230.138.96:26656,4928b2aa3a2c89d2700d3ca1192455aefde74c3c@142.132.202.87:26656,7f46c5c86639e04183cea341d62c59213cdc4542@185.230.138.49:26656,7a2345a2e01f27576b3463a35e3b83f666d191d9@204.93.241.110:27657,7e31ab391f5b5756a75dc18b5275b609c81a34ee@34.122.164.239:26656,20355d8c6b7e8c77576982bfca148b5cbd1a538d@185.230.138.95:26656,ee59d85b081e4051cf447dba5b703636bee77987@65.109.25.62:26656,124c2eaba28bac74c8d7128b923541b303b5241d@185.52.52.30:26656,8b96338b18c1e4a76a119fe0812c131a4e2cc96a@65.109.70.45:20656,daecb368a82196424929479a369ae9ebb9c3e413@185.185.82.234:26656,72ff166996ef9590879a7b7ab00b3b71529632a9@65.109.90.171:31656,354a554c8ba12260c130cc1d5b706b10aced51ab@143.198.206.192:34656,bce4a3976c39d811d50b18ed104b0d04da398591@213.239.207.175:53656,972b18ab3f89bb9148f330693df4e03f2e38cc78@65.108.71.80:26666,13dc844645671d5da8ee81ab969d19166c3df11d@65.109.90.169:15656,7786f708c1851dd433a03f71ec3ff74d65895de7@34.31.130.235:26656,c96b16a81c780d840530c17fec6efd31b329c458@34.133.135.231:26656,3320a6e7d7f1480e832d74d5ada53d8e275458bb@65.108.238.61:24656,8df8a64ecf0aaba1e1faee06d005aa912d578549@65.109.89.5:41656,d82343cb3d168522c54c4ffbfd4415e9b467e806@23.88.51.134:46656,6450606f42fce151ca3897d28ff81a908710f9ff@77.120.115.149:26656,b7084c40af131f24ab7e449a9844e0f56c94fa41@51.91.30.173:4000,31f08ac1a5c3d4bbb9d486e1ea96b298e04625df@65.109.84.33:40656,5869fe239308895eed0cdfdfdf386c7642a36459@38.242.227.84:15656,1413664d3cfa37c2d661f740b2b47105433f3872@65.21.139.155:34656,8dfda1e1a1a690440810d8fdc19c5788ac5a4810@65.109.48.181:33656,d08f56e54945143ed6aff8a8a2d3806ffb6ab389@65.109.99.68:29656,c8171d5b90ea72992408f8cfcd3893256d22aabc@65.109.94.221:40656,1171accc7427f2ffb76fcaa5acdef518ff42c382@178.63.104.200:45656,7e9827b5154a4ec4de93e277b19f77a387661664@46.4.213.198:26656,6d4f2faa284b9c9625e781309b637e92627b6afd@188.40.59.225:45656,958d9056c6173edb4714b6468bda509e97d0c80c@65.108.231.124:45656,447644c34095606449e8f7eb34eeea2d9b7f2216@65.109.225.25:26656,d0a57dec1e14e60e73c9a3f89f7cf351a846bd8a@120.226.39.220:16656,294a03eabd098fe74ab1d5eac97d9fd11684d3db@120.226.39.215:26656"

    
    sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.archway/config/config.toml
    sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.archway/config/config.toml

### 加入网络
##### 获得创世文化
    curl -s https://raw.githubusercontent.com/archway-network/networks/main/constantine-3/genesis.json > $HOME/.archway/config/genesis.json
    curl -s https://snapshots1-testnet.nodejumper.io/archway-testnet/addrbook.json > $HOME/.archway/config/addrbook.json
    
##### 启动节点
    目前需要标志跳过187340才能正确同步节点（constantine-1）：
    archwayd start --x-crisis-skip-assert-invariants --unsafe-skip-upgrades 187340
    
    constantine-3启动节点：
    archwayd start

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
    --amount=4700000000000000000aconst \
    --pubkey=$(archwayd tendermint show-validator) \
    --moniker=$MONIKER \
    --chain-id=constantine-3 \
    --commission-rate="0.01" \
    --commission-max-rate="0.10" \
    --commission-max-change-rate="0.01" \
    --min-self-delegation=1 \
    --fees=200000000000000000aconst \
    --from=$WALLET
<img width="857" alt="微信截图_20230330112130" src="https://user-images.githubusercontent.com/100336530/228720322-795bf2f0-4ee0-4047-a272-f23da3ec586f.png">

## 常用命令
##### 解除监禁
    archwayd tx slashing unjail --chain-id=constantine-3 --from=<your-wallet-address> --gas auto --fees 3000aconst
    
    E.G：
     archwayd tx slashing unjail --chain-id=constantine-3 --from=archway1fvytam6v3qvxfc3wk8a27tk2s5cye4n7840986 --gas 98000 --fees 100000000000000000aconst -y

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
    archwayd tx staking edit-validator --website="<YOUR_WEBSITE>" --details="<YOUR_DESCRIPTION>" --moniker="<YOUR_NEW_MONIKER>" --from =$WALLET --fees 5000aconst

##### 增加担保
    archwayd tx staking delegate <ValidatorAddress> <TOKENS_COUNT>uconst--from $WALLET --fees 5000aconst -y

##### 取消委托
    archwayd tx staking unbond <ValidatorAddress> <TOKENS_COUNT>uconst --from $WALLET --fees 5000aconst -y

##### 发送代币
    archwayd tx bank send $WALLET <WALLET_TO> <TOKENS_COUNT>uconst --fees 5000aconst --gas auto

##### 所有活跃验证者列表
    archwayd q staking validators -o json --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '.tokens + " - " + .description.moniker' | sort -gr | nl

##### 所有不活跃的验证者列表
    archwayd q staking validators -o json --limit=1000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '.tokens + " - " + .description.moniker' | sort -gr | nl





































