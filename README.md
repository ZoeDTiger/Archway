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















































































