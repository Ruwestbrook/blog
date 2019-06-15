---
title: cosmos
date: 2019-06-04 08:29:02
tags:
---

# cosmos矿场搭建

## 安装golang
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
        # 设置go的工作环境: 新建文件夹用于go的工作环境,并且该文件夹需要用src pkg bin三个文件夹  
        mkdir -p $HOME/go
        cd $HOME/go
        mkdir src pkg bin
```
设置系统环境:
```bash   
        #个人喜欢用nano编写
        sudo nano ~/.profile  
        #加入一下:
        export GOROOT=/usr/local/go
        export GOPATH=$HOME/go
        export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
```
使之生效:
```bash   
        source ~/.profile
```
* question:每次使用putty登录系统后,使用go version 都是提示未安装,但是执行" source ~/.profile " 命令后 go version又能打印出版本信息  可能是 ~/.profile保存的是临时变量,(可能需要重启系统)(重启系统后好想没有遇到这个问题了)

到此golang环境安装完成

* tips: 官方文档推荐依次执行以下命令
```bash   
        mkdir -p $HOME/go/bin
        echo "export GOPATH=$HOME/go" >> ~/.bash_profile
        echo "export PATH=\$PATH:\$GOPATH/bin" >> ~/.bash_profile
        source ~/.bash_profile
```


## cosmos环境搭建:
```bash

mkdir -p $GOPATH/src/github.com/cosmos
cd $GOPATH/src/github.com/cosmos

#说明:clone gaia的时候latest-release-tag只有V0.0.0 而且make install会出错
# 只有clone cosmos-sdk的时候 才能指定某个版本版本

#"英文版"安装
git clone -b <latest-release-tag> https://github.com/cosmos/gaia
#"中文版"安装
git clone -b <latest-release-tag> https://github.com/cosmos/cosmos-sdk

cd gaia && make install
```
执行一下命令:
```bash
 gaiad version --long
 gaiacli version --long
```
如果出现类似下方的输出,那么说明安装成功
```bash
cosmos-sdk: 0.35.0
git commit: 028bbef48d093242a2b9adb9bab34eecc97bf3fe
vendor hash:
build tags: netgo ledger
go version go1.12.5 linux/amd64
```

* question:
1. 官方在中文文档和英文文档安装方法不同
1. 按照英文文档执行命令时会提示coomos-sdk有一个版本不存在,不知道什么原因
1. 按照中文文档安装,成功后提示和官方提示类似
1. 再加入测试网络时执行make update_tools install 失败
1. cosmos-sdk和gaia的关系,gaia的介绍中说是以cosmos-sdk的一些模块构成的,但是再实际使用时存在2的提示
1. [相同问题issue] (https://github.com/cosmos/gaia/issues/23)


## cosmos搭建自己的测试环境:
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


## cosmos加入主网络:


```bash
  #填写自己的名字  随意取
  gaiad init <your_custom_moniker>
```

1.需要在配置文件中填写seeds信息(划重点 而且是需要自己去找)

```bash

# 可以在该文件中修改your_custom_moniker 

# 在该文件中找到seeds = ,然后填写你找到的seeds节点 
nanno ~/.gaiad/config/config.toml

```

2.下载Genesis.json文件

```bash


mkdir -p $HOME/.gaiad/config
curl https://raw.githubusercontent.com/cosmos/launch/master/genesis.json > $HOME/.gaiad/config/genesis.json

```

运行

```bash

  gaiad start

```
查看状态

```bash

  gaiacli status

```

question:

1. 文档中'gaiad start --assert-invariants-blockly'无法执行,查看[issue 4053](https://github.com/cosmos/cosmos-sdk/issues/4053) 说是被另一个命令替代了
      eplace gaiad's --assert-invariants-blockly with --inv-check-freq. 实际操纵中使用后面的命令然后添加参数可以运行

1. 关于seeds:我的理解是种子节点,因为产生的数据需要像每一个节点发送,但后如果节点都连接上主网络上的上压力会很大,所以需要用多个节点充当著网络节点以减轻网络压力 
 可以在[launch ](https://github.com/cosmos/launch)界面最下方选择合适的节点

1. 在执行'gaiad start'时,前期往往出现'io time out' 或者  'seeds connect failed'类似的错误,但是程序运行一段事件后没有错误,出现大量的消息,因为是cosmos的交易信息类似的(还不能确定)
 但是在这中间还是会偶尔出现红色的报错

1.  关于 cosmos-1和cosmos-2的区别,在 cosmos-1的文档中说明在验证人投票后升级到cosmos-2     但是两者具体区别不知道



## 加入公共测试网络



## 成为验证人

 * 在公网上成为验证人是否需要拥有atom

 * 如何决解安全问题,例如DDOS攻击