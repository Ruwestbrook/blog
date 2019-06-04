---
title: cosmos
date: 2019-06-04 08:29:02
tags:
---

#cosmos矿场搭建

###安装golang
```bash   
        sudo apt-get update
        sudo apt-get -y upgrade
```
找一个目录,下载galang安装包
```bash   
        #1.11可以替换成你想下载的版本,例如1.12.5
        wget https://dl.google.com/go/go1.12.5.linux-amd64.tar.gz   
```
* tips:下载需要能访问外网,建议用国外服务器,国内服务器无法下载,即使翻墙,接下去的操作也很难执行

解压

```bash   
        sudo tar -xvf go1.12.5.linux-amd64.tar.gz
```
移动到其他目录
```bash   

        sudo mv go /usr/local


设置go的工作环境: 新建文件夹用于go的工作环境,并且该文件夹需要用src pkg bin三个文件夹

```bash   
        mkdir -p $HOME/go
        cd $HOME/go
        mkdir src pkg bin
```
设置系统环境:
```bash   
        #个人喜欢用nano编写
        sudo nano ~/.profile  
```
加入一下:

        export GOROOT=/usr/local/go
        export GOPATH=$HOME/go
        export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

使之生效:
```bash   
        source ~/.profile
```
* question:每次使用putty登录系统后,使用go version 都是提示未安装,但是执行" source ~/.profile " 命令后 go version又能打印出版本信息  可能是 ~/.profile保存的是临时变量,(可能需要重启系统)

到此golang环境安装完成

* tips: 官方文档推荐依次执行以下命令
```bash   
        mkdir -p $HOME/go/bin
        echo "export GOPATH=$HOME/go" >> ~/.bash_profile
        echo "export PATH=\$PATH:\$GOPATH/bin" >> ~/.bash_profile
        source ~/.bash_profile
```


##cosmos环境搭建:
* question:
* 1. 官方在中文文档和英文文档安装方法不同
* 2. 按照英文文档执行命令时会提示coomos-sdk有一个版本不存在,不知道什么原因
* 3. 按照中文文档安装,成功后提示和官方提示类似
* 4. 再加入测试网络时执行make update_tools install 失败


英文版安装:https://hub.cosmos.network/docs/installation.html
中文版安装:https://hub.cosmos.network/zh/installation.html




##cosmos环境自己的测试环境:


### 创建genesis文件并启动网络

```bash
# You can run all of these commands from your home directory
cd $HOME

# Initialize the genesis.json file that will help you to bootstrap the network
gaiad init --chain-id=testing testing

# Create a key to hold your validator account
gaiacli keys add validator

# Add that key into the genesis.app_state.accounts array in the genesis file
# NOTE: this command lets you set the number of coins. Make sure this account has some coins
# with the genesis.app_state.staking.params.bond_denom denom, the default is staking
gaiad add-genesis-account $(gaiacli keys show validator -a) 1000000000stake,1000000000validatortoken

# Generate the transaction that creates your validator
gaiad gentx --name validator

# Add the generated bonding transaction to the genesis file
gaiad collect-gentxs

# Now its safe to start `gaiad`
gaiad start
```


以上代码运行正常,可以开启运行