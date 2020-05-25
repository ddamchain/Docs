## API Table Of Content

* [发送交易](#1发送交易)
* [查询当前链高度](#2查询块高)
* [获取块信息](#3获取块信息)
* [根据交易hash获取交易信息](#4根据交易hash获取交易信息)
* [获取交易收据](#5获取交易收据)
* [获取账户DDAM余额](#6获取账户余额)
* [nonce查询](#7nonce查询)
* [Block详情查询](#8Block详情查询（浏览器专用接口）)
* [获取账户质押金](#9获取账户质押金)

## 1、发送交易

方法：Gx_tx

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情         | 备注                                                      |
| ------- | ---------------- | -------- | ------------ | --------------------------------------------------------- |
| method  | 方法             | string   | Gx_tx        | Gx是namespace，tx是RPC调用的接口名，首字母统一小写        |
| params  | 参数值列表       | json数组 | 参照下方示例 | json数组里参数顺序和类型必须和附表1.1相同，否则会读取失败 |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0          | 固定写法                                                  |
| id      | JSON-RPC协议自带 | uint     | 1            | 固定写法                                                  |

附表1.1

|  字段名  |  类型  |     含义     | 默认值 | 是否必须 | 备注                                                         |
| :------: | :----: | :----------: | :----: | :------: | ------------------------------------------------------------ |
|  Target  | string |   收款地址   |        |    是    | 66字节的16进制字符串                                         |
|  Value   | uint64 |   转账金额   |   0    |    否    |                                                              |
|   Gas    | uint64 |   gas限制    |        |    是    | 默认值是3000                                                 |
| GasPrice | uint64 | gas金额大小  |        |    是    | 默认值是500RA                                                |
|  TxType  | uint64 |   交易类型   |   0    |    是    | 具体交易类型见附表1.1.1                                      |
|  Nonce   | uint64 | 账号交易次数 |   0    |    是    | 为该账号的转账计数，每转账一次，该账号Nonce+1，可通过Gx_nonce查询（返回的是交易次数+1，即下次交易可以直接使用的nonce值） |
|   Data   | []byte | 交易数据信息 |   ""   |    否    | 具体类型见附表1.1.1                                          |
|   Sign   | string |  付款方签名  |  nil   |    是    | 签名时对以上7种数据进行签名,直接调用SDK的sign接口实现        |

附表1.1.1

| 交易类型（TxType） | 含义                                                         | 交易数据信息（Data）                                         | 类型   |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------ |
| 0                  | 普通转账交易                                                 | 普通转账时为空                                               | string |
| 1                  | 绑定交易，矿工address绑定UMID                                | 此类型中Data内容为UMID（json序列化后转成string）， target字段为绑定的矿工address | string |
| 2                  | 转绑交易，矿工转出自己的矿机给其他矿工绑定                   | 此类型中Data内容为转出的矿工地址+umid（json序列化后转成string）， target字段为转入的矿工address | string |
| 3                  | 解绑交易，管理员解除矿工和其矿机的绑定关系                   | 此类型中Data内容为UMID（json序列化后转成string）， target字段为绑定的矿工address | string |
| 4                  | 增加质押，矿工发起质押交易，可以是开始，也可以是追加         |                                                              |        |
| 5                  | 减少质押，矿工发起质押交易，可以减少任意质押金额（减少金额<=总质押金额） |                                                              |        |
|                    |                                                              |                                                              |        |



参数示例：

```json
{
"method": "Gx_tx",
"params": [
    "{\"target\":\"DD3240f6ac9f92aaee6183b3d3da603622f9c2481d49d88010954369e3e2882c5f\",\"value\":1000,\"gas\":10000,\"gasprice\":10000,\"tx_type\":0,\"nonce\":2,\"data\":null,\"sign\":\"0xb2bc6267d8a87c3ad893fcb66bf9d792a9e873a95c6c5558be9f59997befc9c6521dbcd1c51942f8c77b6efaa3a69e82dc7b409b02aaa92e739b1766a77d3dd501\"}"
],
"jsonrpc": "2.0",
"id": 1
}
```



返回值：`result` 中 `data` 即为该交易的 hash

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | string | 见附表1.2 |          |

附表1.2

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 0:成功，-1:失败，                                            |
| data    | string  | 0x开头的66字节的16进制字符串                                 |

返回值示例：

```json
{
"jsonrpc": "2.0",
"id": 1,
"result":	{
		"message": "success",
		"status": 0,
		"data": "0x5a50275d85ff4de813c8d0175e290a79152b3fd84be799c601a131119c86c501",
	}
}
```



## 2、查询块高

方法：Gx_blockHeight

接口方法：Post

数据类型：json

返回类型：json

参数：无

| 方法名  | 含义             | 类型     | 详情           | 备注                                        |
| ------- | ---------------- | -------- | -------------- | ------------------------------------------- |
| method  | 方法             | string   | Gx_blockHeight | Gx是namespace，blockHeight是RPC调用的接口名 |
| params  | 交易json串       | json数组 | 空             |                                             |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0            | 固定写法                                    |
| id      | JSON-RPC协议自带 | uint     | 1              | 固定写法                                    |

参数示例：

```json
{
"method": "Gx_blockHeight",
"params": [],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表2.1 |          |

附表2.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 成功：0，失败：-1                                            |
| data    | float64 | 返回的数表示具体块高度                                       |

```+json
{
"jsonrpc": "2.0",
"id": 1,
"result":	{
		"message": "success",
		"status": 0,
		"data": 250
	}
}
```



## 3、获取块信息

#### 1）根据块高度查询

方法：Gx_getBlockByHeight

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情                | 备注                                             |
| ------- | ---------------- | -------- | ------------------- | ------------------------------------------------ |
| method  | 方法             | string   | Gx_getBlockByHeight | Gx是namespace，getBlockByHeight是RPC调用的接口名 |
| params  | 交易json串       | json数组 | 10                  | 表示要查询的目标块高度                           |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0                 | 固定写法                                         |
| id      | JSON-RPC协议自带 | uint     | 1                   | 固定写法                                         |

```json
{
"method": "Gx_getBlockByHeight",
"params": [10],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：`result` 中 `data` 即为块的详细信息

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表3.1 |          |

附表3.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 成功：0，失败：-1                                            |
| data    | json    | 返回block的内容，具体见附表3.1.1                             |

附表3.1.1

| **字段**              | **含义**                  | 类型    | 备注                                         |
| --------------------- | ------------------------- | ------- | -------------------------------------------- |
| Height`               | 块高度                    | float64 | 块高度就是目前块的序号                       |
| Hash`                 | 块hash                    | string  | 表示本区块的hash，根据此hash可以查询到区块的 |
| pre_hash`             | 前一块hash                | string  |                                              |
| CurTime`              | 出块时间                  | string  |                                              |
| Proposer`             | 铸块矿工地址              | string  |                                              |
| Nonce`                | 随机数                    | float64 |                                              |
| TxTree`               | 交易merkel tree根         | string  |                                              |
| ReceiptTree`          | 交易执行收据merkel tree根 | string  |                                              |
| StateTree`            | 账户状态merkel tree根     | string  |                                              |
| BaseTarget`           | POC的目标值               | float64 |                                              |
| CumulativeDifficulty` | 累计总难度                | float64 |                                              |
| Auth`                 | 设备鉴权码                | string  | UMID+address+blockhash再hash生成的字段       |
| Sign                  | 铸块矿工签名              | string  |                                              |



#### 2）根据块hash查询

方法：Gx_getBlockByHash

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情                                                         | 备注                                           |
| ------- | ---------------- | -------- | ------------------------------------------------------------ | ---------------------------------------------- |
| method  | 方法             | string   | Gx_getBlockByHash                                            | Gx是namespace，getBlockByHash是RPC调用的接口名 |
| params  | 交易json串       | json数组 | "0x1dd7b718e45505e379a106912312d647a1da4c9123aeffa44f7ec2bd283ffec9" | 表示要查询的目标块hash                         |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0                                                          | 固定写法                                       |
| id      | JSON-RPC协议自带 | uint     | 1                                                            | 固定写法                                       |

```+json
{
"method": "Gx_getBlockByHash",
"params": ["0x1dd7b718e45505e379a106912312d647a1da4c9123aeffa44f7ec2bd283ffec9"],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：`result` 中 `data` 即为块的详细信息（具体结构同上Gx_getBlockByHeight返回的结构相同）

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表3.2 |          |

附表3.2

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 成功：0，失败：-1                                            |
| data    | json    | 返回block的内容，具体见附表3.1.1                             |



## 4、根据交易hash获取交易信息

方法：Gx_transDetail

接口方法：Post

数据类型：json

返回类型：json

参数：交易hash

| 方法名  | 含义             | 类型     | 详情                                                         | 备注                                        |
| ------- | ---------------- | -------- | ------------------------------------------------------------ | ------------------------------------------- |
| method  | 方法             | string   | Gx_transDetail                                               | Gx是namespace，transDetail是RPC调用的接口名 |
| params  | 交易json串       | json数组 | "0x2edd6a1c6c694bcbc48bee73c07f0e622cf9a2b11c3c98931db0837b79ecf1ab" | 表示要查询的目标交易的hash                  |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0                                                          | 固定写法                                    |
| id      | JSON-RPC协议自带 | uint     | 1                                                            | 固定写法                                    |

```json
{
"method": "Gx_transDetail",
"params": ["0x2edd6a1c6c694bcbc48bee73c07f0e622cf9a2b11c3c98931db0837b79ecf1ab"],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：`result` 中 `data` 即为该交易的详细信息

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表4.1 |          |

附表4.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 0:成功，-1:失败，                                            |
| data    | json    | 返回交易信息，具体交易信息附表4.1.1                          |

附表4.1.1

| 返回参数  | 含义                     | 类型    | 备注                |
| --------- | ------------------------ | ------- | ------------------- |
| data      | 发送不同交易时的Data内容 | string  | 具体类型见附表4.1.1 |
| gas_limit | gas限制                  | float64 |                     |
| gas_price | gas金额                  | float64 |                     |
| hash      | 这笔交易的hash           | string  |                     |
| nonce     | 此次交易在此账号中的次数 | float64 |                     |
| source    | 付款方 address           | string  |                     |
| target    | 收款方address            | string  |                     |
| type      | 交易类型                 | float64 | 具体类型见附表4.1.1 |
| value     | 交易金额                 | float64 |                     |

附表4.1.1

| 交易类型（type） | 含义                                       | 交易数据信息（data）                                         | 类型（data） |
| ---------------- | ------------------------------------------ | ------------------------------------------------------------ | ------------ |
| 0                | 普通转账交易                               | 普通转账时为空                                               | string       |
| 1                | 绑定交易，矿工address绑定UMID              | 此类型中Data内容为UMID（32字节）， target字段为绑定的矿工address | string       |
| 2                | 转绑交易，矿工转出自己的矿机给其他矿工绑定 | 此类型中Data内容为转出的矿工地址+umid（32字节）， target字段为转入的矿工address | string       |

返回值示例：

```json
{
"jsonrpc": "2.0",
"id": 1,
"result":	{
		"message": "success",
		"staus": 0,
		"data": {
				"data": "aGVsbG8gd29ybGQ=",
				"gas_limit": 3000,
				"gas_price": 500,
				"hash": "0x2edd6a1c6c694bcbc48bee73c07f0e622cf9a2b11c3c98931db0837b79ecf1ab",
				"nonce": 99,
				"source": "DDe75051bf0048decaffa55e3a9fa33e87ed802aaba5038b0fd7f49401f5d8b019",
				"target": "DDed890e78fc5d07e85e66b7926d8370c095570abb5259e346438abd3ea7a56a8a",
				"type": 0,
				"value": 10
		}
	}
}
```



## 5、获取交易收据

方法：Gx_txReceipt

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情         | 备注                                                 |
| ------- | ---------------- | -------- | ------------ | ---------------------------------------------------- |
| method  | 方法             | string   | Gx_txReceipt | Gx是namespace，tx是RPC调用的接口名，首字母统一小写， |
| params  | 参数值列表       | json数组 | 参照下方示例 | 发送交易会返回此笔交易的hash值，为66字节的16进制     |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0          | 固定写法                                             |
| id      | JSON-RPC协议自带 | uint     | 1            | 固定写法                                             |

```json
{
"method": "Gx_txReceipt",
"params": ["0xfcf0ead9f48e395f3a00dddc966f76b3124526eee267cf39401644a6c5f97604"],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表5.1 |          |

附表5.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 0:成功，-1:失败，                                            |
| data    | json    | 返回收据具体信息包括交易信息，交易信息见附表4.1.1，收据信息具体见附表5.1.1 |

附表5.1.1

| 字段              | 含义                                                         | 类型    | 备注                                                         |
| ----------------- | ------------------------------------------------------------ | ------- | ------------------------------------------------------------ |
| cumulativeGasUsed | 本交易实际消耗的gas数量                                      | float64 |                                                              |
| height            | 本交易所在块高                                               | float64 |                                                              |
| status            | 交易执行状态，0执行成功， 1执行失败，2余额不足，3交易解析不通过（具体见备注） | float64 | status返回3有以下可能：1、普通交易无表示；2、绑定交易表示交易data字段中的umid长度错误；3、转绑交易表示data字段中的地址长度或umid长度错误；4、解绑交易表示交易data字段中的umid长度错误；5、增加质押交易表示value为空或者target != source；6、减少质押交易表示value为空或者target != source，或者已经是0质押 |
| transactionHash   | 本次交易的hash                                               | string  |                                                              |
| tx_index          | 交易在块中的索引                                             | float64 |                                                              |

返回值示例：

```+json
{
"jsonrpc": "2.0",
"id": 1,
"result":	{
		"message": "success",
		"staus": 0,
		"data": {
				"receipt": {
						"cumulativeGasUsed": 447,
						"height": 25314,
						"status": 0,
						"transactionHash": "0x2edd6a1c6c694bcbc48bee73c07f0e622cf9a2b11c3c98931db0837b79ecf1ab",
			"tx_index": 0
				}
				"transaction": {
						"data": "aGVsbG8gd29ybGQ=",
						"gas_limit": 3000,
						"gas_price": 500,
						"hash": "0x2edd6a1c6c694bcbc48bee73c07f0e622cf9a2b11c3c98931db0837b79ecf1ab",
						"nonce": 99,
						"source": "DDe75051bf0048decaffa55e3a9fa33e87ed802aaba5038b0fd7f49401f5d8b019",
						"target": "DDed890e78fc5d07e85e66b7926d8370c095570abb5259e346438abd3ea7a56a8a",
						"type": 0,
						"value": 10
				}
		}
}
}
```

## 6、获取账户余额

方法：Gx_balance

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情         | 备注                                                    |
| ------- | ---------------- | -------- | ------------ | ------------------------------------------------------- |
| method  | 方法             | string   | Gx_balance   | Gx是namespace，balance是RPC调用的接口名，首字母统一小写 |
| params  | 参数值列表       | json数组 | 参照下方示例 | 表示66字节的16进制账户地址                              |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0          | 固定写法                                                |
| id      | JSON-RPC协议自带 | uint     | 1            | 固定写法                                                |

参数示例：

```json
{
"method": "Gx_balance",
"params": ["DDe75051bf0048decaffa55e3a9fa33e87ed802aaba5038b0fd7f49401f5d8b019"],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表6.1 |          |

附表6.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 0:成功，-1:失败，                                            |
| data    | float64 | 返回目标账号的剩余金额                                       |

```json
{
"jsonrpc": "2.0",
"id": 1,
"result":	{
		"message": "The balance of account: DDe75051bf0048decaffa55e3a9fa33e87ed802aaba5038b0fd7f49401f5d8b019 is 251 DDAM",
		"staus": 0,
		"data": 251
	}
}
```

## 7、nonce查询

方法：Gx_nonce

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情         | 备注                                                  |
| ------- | ---------------- | -------- | ------------ | ----------------------------------------------------- |
| method  | 方法             | string   | Gx_nonce     | Gx是namespace，nonce是RPC调用的接口名，首字母统一小写 |
| params  | 参数值列表       | json数组 | 参照下方示例 | 参数                                                  |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0          | 固定写法                                              |
| id      | JSON-RPC协议自带 | uint     | 1            | 固定写法                                              |

```+json
{
"method": "Gx_nonce",
"params": ["DDe75051bf0048decaffa55e3a9fa33e87ed802aaba5038b0fd7f49401f5d8b019"],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表7.1 |          |

附表7.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 0:成功，-1:失败，                                            |
| data    | float64 | 返回数据为该账号地址发生过的交易次数                         |

```json
{
"jsonrpc": "2.0",
"id": 1,
"result":	{
		"message": "success",
		"staus": 0,
		"data": 11
	}
}
```



## 8、Block详情查询（浏览器专用接口）

方法：Explorer_explorerBlockDetail

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情                         | 备注                                                         |
| ------- | ---------------- | -------- | ---------------------------- | ------------------------------------------------------------ |
| method  | 方法             | string   | Explorer_explorerBlockDetail | Explorer是namespace，explorerBlockDetail是RPC调用的接口名，首字母统一小写 |
| params  | 参数值列表       | json数组 | 参照下方示例                 | 参数                                                         |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0                          | 固定写法                                                     |
| id      | JSON-RPC协议自带 | uint     | 1                            | 固定写法                                                     |

```json
{
"method": "Explorer_explorerBlockDetail",
"params": [10],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表8.1 |          |

见附表8.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 成功：0，失败：-1                                            |
| data    | json    | 返回blockdetail的内容，具体见附表8.1.1                       |

附表8.1.1

| **字段**        | **含义**       | 类型     | 备注              |
| --------------- | -------------- | -------- | ----------------- |
| BlockDetail     |                |          | 具体见附表8.1.1.1 |
| Receipts        | 成功交易的收据 | json数组 | 具体见附表8.1.1.2 |
| EvictedReceipts | 失败交易的收据 | json数组 | 具体见附表8.1.1.2 |

附表8.1.1.1

| **字段**   | **含义**   | 类型     | 备注                |
| ---------- | ---------- | -------- | ------------------- |
| Block      | 块详细信息 |          | 具体见附表8.1.1.1-a |
| Trans      | 交易信息   | json数组 | 具体见附表8.1.1.1-b |
| PreTotalQN | 权重       | float64  |                     |

附表8.1.1.2

| **字段**          | **含义**       | 类型    | 备注 |
| ----------------- | -------------- | ------- | ---- |
| Status            | 交易状态       | float64 |      |
| CumulativeGasUsed | 累计总消耗gas  | float64 |      |
| TxHash            | 交易hash       | string  |      |
| Height            | 交易所在块高   | float64 |      |
| TxIndex           | 块内交易的下标 | float64 |      |

附表8.1.1.1-a

| **字段**             | **含义**                  | 类型    | 备注                                         |
| -------------------- | ------------------------- | ------- | -------------------------------------------- |
| Hash                 | 块hash                    | string  | 表示本区块的hash，根据此hash可以查询到区块的 |
| Height               | 块高度                    | float64 | 块高度就是目前块的序号                       |
| Hash                 | 块hash                    | string  | 表示本区块的hash，根据此hash可以查询到区块的 |
| Pre_hash             | 前一块hash                | string  |                                              |
| CurTime              | 出块时间                  | string  |                                              |
| Proposer             | 铸块矿工地址              | string  |                                              |
| Nonce                | 随机数                    | float64 |                                              |
| TxTree               | 交易merkel tree根         | string  |                                              |
| ReceiptTree          | 交易执行收据merkel tree根 | string  |                                              |
| StateTree            | 账户状态merkel tree根     | string  |                                              |
| BaseTarget           | POC的目标值               | float64 |                                              |
| CumulativeDifficulty | 累计总难度                | float64 |                                              |
| Difficulty           | 当前块高难度              | float64 |                                              |
| Capacity             | 容量                      | string  |                                              |
| Auth                 | 设备鉴权码                | string  | UMID+address+blockhash再hash生成的字段       |

附表8.1.1.1-b

| **字段** | **含义**     | 类型    | 备注 |
| -------- | ------------ | ------- | ---- |
| Data     | 交易数据信息 | string  |      |
| Value    | 交易金额     | float64 |      |
| Nonce    | 账号交易次数 | float64 |      |
| Source   | 交易发送方   | string  |      |
| Target   | 交易接收方   | string  |      |
| Type     | 交易类型     | float64 |      |
| GasLimit | gash现实     | float64 |      |
| GasPrice | gas金额大小  | float64 |      |
| Hash     | 交易hash     | string  |      |

参数示例：

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "message": "success",
        "status": 0,
        "data": {
            "height": 10,
            "hash": "0x4c42611ccd967aec2b9600aa612cb58854769eeaf732e5e5b4c1ae6288845ccb",
            "pre_hash": "0x5271fe25ddca3215caebda2091cd68cd7974d2a6b6f7c691143c95b982264177",
            "cur_time": "2019-08-13T17:59:41+08:00",
            "proposer": "DD0d2ca033becaee1dfd68798c0d855af235ca5ae7e8fdcefa8a7af075aa3e359d",
            "nonce": 260655,
            "tx_tree": "0x82542fe7eea71710db9fe1024f0f61979f3a7cdcf125d838cfbedde2305f2668",
            "receipt_tree": "0x486fe7634a3c81f871c0b8611f1260c07c5b3625cae2f4fe1f49edcd37307435",
            "state_tree": "0xfe4e28cf2841bb86611fe4cd270513c584953ad7886807df0796bdccd0c4b99a",
            "base_target": 13996894436,
            "cumulative_difficulty": 7588635649,
            "difficulty": 1317916924,
            "capacity": "39GB",
            "auth": "hRn2YkpK4KxODDOG/TEyIxt/uRWzsZu++QuXxRFWYeU=",
            "trans": [
                {
                    "data": "GsdZmVfXw7f+LZnqMD6OJotWTthc/3JjFhCQ/SIMXvw=",
                    "value": 0,
                    "nonce": 1,
                    "source": "DD12c503e93a8a6348333ce043b825e3ae714b5be6785bc580b5e6743d5decb245",
                    "target": "DDa6c1106469d59abaca375504f232738ef6cc7750f1e330cb1708cb275e8965e1",
                    "type": 1,
                    "gas_limit": 10000,
                    "gas_price": 10000,
                    "hash": "0x7d5445472deb091ae586a0bfb8c606b030316c5c94887d553b36fb0413bf0e4e"
                }
            ],
            "pre_total_qn": 0,
            "receipts": [
                {
                    "status": 0,
                    "cumulativeGasUsed": 842,
                    "transactionHash": "0x7d5445472deb091ae586a0bfb8c606b030316c5c94887d553b36fb0413bf0e4e",
                    "height": 10,
                    "tx_index": 0
                }
            ],
            "evictedReceipts": []
        }
    }
}
```

```json
{
    "jsonrpc": "2.0",
    "id": 1,
    "result": {
        "message": "success",
        "status": 0,
        "data": {
            "height": 571,
            "hash": "0x9bcf842d5a1248d4a1f2b160fe224a1700a12804b6d7045795e109d2972a140e",
            "pre_hash": "0x5600c8717650a02f6b8d8e432daf0eaf543dcd79615909c51e11891178dccd7b",
            "cur_time": "2019-09-08T01:29:06+08:00",
            "proposer": "0x0d2ca033becaee1dfd68798c0d855af235ca5ae7e8fdcefa8a7af075aa3e359d",
            "nonce": 4772,
            "tx_tree": "0x20ccdde9804d292822b8ee057d9509b4de2b6550fa7881265f7b9b6ed92dc3e6",
            "receipt_tree": "0xcddc565bf1a5bb0feadde2908c0b085ed88051d9c05a35bdb476bdaac9b4bc5c",
            "state_tree": "0xbf33d29d1c23f3056f65f36c47ce30f975dcace3ce20b01db46040d767918a16",
            "base_target": 9006597493176,
            "cumulative_difficulty": 13664737312,
            "difficulty": 2048136,
            "capacity": "6GB",
            "auth": "N111YtYMhQetbiBusWpVFCrSzKvKwuJcVURuYHJwjMI=",
            "trans": [
                {
                    "data": "",
                    "value": 1000,
                    "nonce": 3,
                    "source": "DD0d2ca033becaee1dfd68798c0d855af235ca5ae7e8fdcefa8a7af075aa3e359d",
                    "target": "DD0d2ca033becaee1dfd68798c0d855af235ca5ae7e8fdcefa8a7af075aa3e359d",
                    "type": 4,
                    "gas_limit": 3000,
                    "gas_price": 500,
                    "hash": "0x16bb121be3ddccf561900b6810d511b8cc087b42a3f2bbe417dc086235f2e013"
                },
                {
                    "data": "",
                    "value": 5000,
                    "nonce": 4,
                    "source": "DD0d2ca033becaee1dfd68798c0d855af235ca5ae7e8fdcefa8a7af075aa3e359d",
                    "target": "DD12c503e93a8a6348333ce043b825e3ae714b5be6785bc580b5e6743d5decb245",
                    "type": 0,
                    "gas_limit": 3000,
                    "gas_price": 500,
                    "hash": "0xb05486e8c5ca7591cce0a390844de5736c8647b09970b362250dbb1a9effeee7"
                }
            ],
            "pre_total_qn": 0,
            "receipts": [
                {
                    "status": 0,
                    "cumulativeGasUsed": 781,
                    "transactionHash": "0x16bb121be3ddccf561900b6810d511b8cc087b42a3f2bbe417dc086235f2e013",
                    "height": 571,
                    "tx_index": 0
                },
                {
                    "status": 0,
                    "cumulativeGasUsed": 781,
                    "transactionHash": "0xb05486e8c5ca7591cce0a390844de5736c8647b09970b362250dbb1a9effeee7",
                    "height": 571,
                    "tx_index": 1
                }
            ],
            "evictedReceipts": []
        }
    }
}
```





## 9、获取账户质押金

方法：Gx_stake

接口方法：Post

数据类型：json

返回类型：json

参数：

| 方法名  | 含义             | 类型     | 详情         | 备注                                                  |
| ------- | ---------------- | -------- | ------------ | ----------------------------------------------------- |
| method  | 方法             | string   | Gx_tstake    | Gx是namespace，stake是RPC调用的接口名，首字母统一小写 |
| params  | 参数值列表       | json数组 | 参照下方示例 | 表示66字节的16进制账户地址                            |
| jsonrpc | JSON-RPC协议自带 | string   | 2.0          | 固定写法                                              |
| id      | JSON-RPC协议自带 | uint     | 1            | 固定写法                                              |

参数示例：

```json
{
"method": "Gx_stake",
"params": ["DD0d2ca033becaee1dfd68798c0d855af235ca5ae7e8fdcefa8a7af075aa3e359d"],
"jsonrpc": "2.0",
"id": 1,
}
```

返回值：

| 方法名  | 含义             | 类型   | 详情      | 备注     |
| ------- | ---------------- | ------ | --------- | -------- |
| jsonrpc | JSON-RPC协议自带 | string | 2.0       | 固定写法 |
| id      | JSON-RPC协议自带 | uint   | 1         | 固定写法 |
| result  | 返回的数据       | json   | 见附表6.1 |          |

附表6.1

| 返回值  | 类型    | 备注                                                         |
| ------- | ------- | ------------------------------------------------------------ |
| message | string  | status为0时，message返回为success；status为-1时，message返回具体失败信息 |
| status  | float64 | 0:成功，-1:失败，                                            |
| data    | float64 | 返回目标账号的质押金额                                       |

```json
{
"jsonrpc": "2.0",
"id": 1,
"result":	{
		"message": "The stake of account: DD0d2ca033becaee1dfd68798c0d855af235ca5ae7e8fdcefa8a7af075aa3e359d is 1000 DDAM",
		"staus": 0,
		"data": 1000
	}
}

```

