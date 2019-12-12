# DDAM RPC 节点启动

## 1. 生成节点账户地址

```shell
# 首先进入节点控制台
shell> ./ddam console

# 输入 help 命令查看所有帮助信息
shell> help

# 创建账户
shell> newaccount -password YOUR_SECRET

Output:
{
    "message": “success",
    "status": 0,

    // 生成的账户地址
    “data": “DDabcdef0123456789…”
}

# 退出控制台
shell> exit
```

## 2. xconf.ini 配置

```
[core]
gasprice_lower_bound = 300
db_file = xdata

[consensus]
max_mem_per_worker = 1GB
worker_num = 2

[net]
# 固定值
seed_id = DDc1dab43c5e912eb1cbe25d1d1eee8e2e01a77d114ce1b51f4e2b9e3fa2cda9a3,DDc401f9fdac56999ed47d26870be158a52127481d838c39907fae1af108e41b25,DDd7531f6c1ea42414fa1f5c10f818738c67e3fe0b55def4c0dae888741003676c

[ddam]
miner = 上一步生成的账户地址
```

## 3. start.sh 启动脚本

```shell
#!/bin/sh

nohup ./ddam miner --config xconf.ini --keystore keystore --password YOUR_SECRET --rpc 2 --rpcport 8101 --nat nat.ddam.one --natport 3300 &
```

## 4. 启动 RPC 节点

```shell
# RPC 默认监听 8101 端口
shell> ./start.sh
```
