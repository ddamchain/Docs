# DDAMChain Transfer Watch

* 1. 通过 RPC:Explorer_explorerBlockDetail 接口获取交易列表

```
{
    "data": {
            "height": 3939,
            "hash": "0x104e793e9d5f77063191a3e15abb91d10c5e8eedd2fd2d15d7ab072d2053f25f",
            "pre_hash": "0x4e60b16d98b9bb304d8e6eca628fa05094574f39463573b76aae218eacc66a66",
            "cur_time": "2019-12-11T17:22:00+08:00",
            "proposer": "DD79690337e21e1950929dded8f2c4f0cf5b09dc9d573f1361c96d829debd4e453",
            "nonce": 580800511,
            "tx_tree": "0xc3dbca19c652caeff54e1443c274e8c008234f3418d2b8a5afe198f2294a3ed3",
            "receipt_tree": "0x6676af158c57d25069fd5296a70a820f71c00d949499279cc405682bafc00af5",
            "state_tree": "0x394577782a3f74fe4af30d2e7c84814c2f1397810d6fc11a71e13c94327afda1",
            "base_target": 3772954,
            "cumulative_difficulty": 12346582228588824,
            "difficulty": 4889204605651,
            "capacity": "13.8960PB",
            "auth": "ob+dh8RDSNCdRVtu2aat+Jdn32iM067dJY+Slbj83d0=",
            "sign": "0OKwJE/45V2ub160mw4GF8m96N2mg2MSlEUjV9CKPmwy74KAgOmWI3eCtV7vZT2iYamzohbXubut3dkRMG7uDAE=",
            "trans": [
                {
                    "data": null,
                    "value": 4.9,
                    "nonce": 1,
                    "source": "DD8b39bdd13485f7b15ed7a03fc07370529fb2c606ad042fd568d33e91c351dce3",
                    "target": "DD98abdfb612674c9a4726a56d88393832b8ea512e1706ff3f6321392141b683af",
                    "type": 0,
                    "gas_limit": 5000,
                    "gas_price": 500,
                    "hash": "0xc4420bb5f30b2f3310434ffc709a5beb3d46eab4f1a61ed2cfb0784db1a8e9fa"
                },
                {
                    "data": null,
                    "value": 13.3,
                    "nonce": 1,
                    "source": "DDafedc777693eba1cc7fbfb17382d2c8cddf891ccd1a8d42966c4d7b0f117c03b",
                    "target": "DDf4638ec912e9bb86c7e3ccef62e7bfb0f1fb215869d826ebe801d313c23c9562",
                    "type": 0,
                    "gas_limit": 5000,
                    "gas_price": 500,
                    "hash": "0xaed57f4fa7e82a5433cd1fd6d957de068f9f1b843c1b7e2b0bf732af2f56991e"
                }
            ],
            "receipts": [
                {
                    "status": 0,
                    "cumulativeGasUsed": 781,
                    "transactionHash": "0xc4420bb5f30b2f3310434ffc709a5beb3d46eab4f1a61ed2cfb0784db1a8e9fa",
                    "height": 3939,
                    "tx_index": 0
                },
                {
                    "status": 0,
                    "cumulativeGasUsed": 781,
                    "transactionHash": "0xaed57f4fa7e82a5433cd1fd6d957de068f9f1b843c1b7e2b0bf732af2f56991e",
                    "height": 3939,
                    "tx_index": 1
                }
            ]
}
```

* 2. 分析块中的交易是否属于某钱包
```
    tx.target === Your_Address And tx.type === 0
```
* 3. 判断交易回执`status`属性
```
    if (status === 0) {
      // 交易成功
    }
    else {
      // 交易失败
    }
```

* 4. 例子

```Javascript
  const rpc = require('ddamrpc-sdk')

  const YOUR_ADDRESS = 'DDf4638ec912e9bb86c7e3ccef62e7bfb0f1fb215869d826ebe801d313c23c9562'
  const BLOCK_HEIGHT = 443

  rpc.Explorer_explorerBlockDetail(BLOCK_HEIGHT).then(({ data }) => {
    const { trans, receipts } = data;
    trans.forEach((tx, idx) => {
      if (tx.type === 0 && tx.target === YOUR_ADDRESS) {
        const receipt = receipts[idx]
        if (receipt.status === 0) {
           // 充值成功业务处理
        }
        else {
          // 充值失败业务处理
        }
      }
    })
  })
```
