# learning ethereum

<https://book.ethereum-jp.net/>

## geth

<https://github.com/ethereum/go-ethereum>

Ethereumの仕様を実装したCUIクライアント

```sh
$ go install github.com/ethereum/go-ethereum/cmd/geth
$ geth version
Geth
Version: 1.10.6-stable
```

## プライベートネットワークに接続する

- genesisブロックの初期化
  - ネットワークでやり取りされるブロックチェーンの最初(Block番号0)のブロックであるGenesisブロックの情報を記述したファイル
  - myGenesis.jsonファイルを作成(手で作成)
    ```json
    {
      "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip150Block": 0,
        "eip155Block": 0,
        "eip158Block": 0,
        "byzantiumBlock": 0,
        "constantinopleBlock": 0,
        "petersburgBlock": 0,
        "istanbulBlock": 0,
        "berlinBlock": 0
      },
      "nonce": "0x0000000000000042",
      "timestamp": "0x0",
      "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "extraData": "",
      "gasLimit": "0x8000000",
      "difficulty": "0x4000",
      "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "coinbase": "0x3333333333333333333333333333333333333333",
      "alloc": {}
    }
    ```
  - 初期化
    ```sh
    geth --datadir eth_private_net init eth_private_net/myGenesis.json
    ```

- プライベートネットワークで起動する(コンソール)
  ```sh
  geth --networkid 15 --nodiscover --datadir eth_private_net console 2>> eth_private_net/geth_err.log
  ```
    - `--networkid` idを指定することでプライベートネットワークを起動できる、指定しないと本番が起動する(ライブネット/メインネットという)(jsonのchainIdと同じidを指定する)
    - `--nodiscover` 同一ネットワーク内のノードの探索を無効にする

- genesisブロックを確認する
  ```sh
  > eth.getBlock(0)
  {
    difficulty: 16384,
    extraData: "0x",
    gasLimit: 134217728,
    gasUsed: 0,
    hash: "0x7b2e8be699df0d329cc74a99271ff7720e2875cd2c4dd0b419ec60d1fe7e0432",
    logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    miner: "0x3333333333333333333333333333333333333333",
    mixHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
    nonce: "0x0000000000000042",
    number: 0,
    parentHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
    receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    size: 507,
    stateRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    timestamp: 0,
    totalDifficulty: 16384,
    transactions: [],
    transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    uncles: []
  }
  ```

- プライベートネットワークで起動する
  ```sh
  geth --networkid 15 --nodiscover --datadir eth_private_net 2>> eth_private_net/geth_err.log
  ```

- 立ち上げたネットワークにアタッチする(コンソールが開く)
  ```sh
  geth --datadir eth_private_net attach
  ```

## etherを採掘する

### 用語

- アカウント
  - EOA(Externally Owned Account)
    - ユーザーによりコントロールされる
  - Contract(コントラクト)
    - 一種のクラスのようなもの
      - EOAによりContractのメソッドが呼ばれる
      - そのメソッドが実行されるとContractの持つクラス変数が書き換えられる
- etherbase(coinbase)
  - 各ノードで採掘を行う際にその報酬を紐づけるEOAのアドレス
- 通貨単位
  - 1 ether = 1,000 finney
  - 1 ether = 1,000,000 szabo
  - 1 ether = 1,000,000,000,000,000,000 wei

### アカウント作成

- アカウント作成
  ```sh
  > personal.newAccount("password")
  "0x2d8296bfec1444d6b6d69ba15132f1441144747e"
  > personal.newAccount("password2")
  "0xe408c887841b62d92469fd3b3582e5174d770ae2"
  ```

- アカウント一覧表示
  ```sh
  > eth.accounts
  ["0x2d8296bfec1444d6b6d69ba15132f1441144747e", "0xe408c887841b62d92469fd3b3582e5174d770ae2"]
  ```

- etherbase確認
  ```sh
  > eth.coinbase
  "0x5cbe920570c1ff6fca2e577d78c1632ec330ab77"
  > eth.coinbase == eth.accounts[0]
  true
  ```

### 採掘する

- 採掘する
  ```sh
  > miner.start()
  null
  ```

- 採掘中か確認
  ```sh
  > eth.mining
  true
  ```

- 採掘を終了
  ```sh
  > miner.stop()
  null
  ```

- 採掘されているブロックを確認する
  ```sh
  > eth.blockNumber
  90
  ```

- 現在の採掘処理のハッシュレートを確認する
  ```sh
  > eth.hashrate
  125496
  ```

- 採掘したブロックの内容を調べる
  ```sh
  > eth.getBlock(90)
  {
    difficulty: 136850,
    extraData: "0xd983010a06846765746888676f312e31362e358664617277696e",
    gasLimit: 122919640,
    gasUsed: 0,
    hash: "0xa37af87eee1f8afd5b69a08e9efeebe317f9d1ec86b75bffed7389b52e0119f7",
    logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
    miner: "0x2d8296bfec1444d6b6d69ba15132f1441144747e",
    mixHash: "0xb79f43ac28f36a306c4398ec5f1c04fd3fad797d93c9338721db659b79c95167",
    nonce: "0x6427f151a3957e95",
    number: 90,
    parentHash: "0x2b525eb74eb895a5bca47a88fe32cec05e5e28c0f59ed70a9c1b952eab079c25",
    receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
    size: 538,
    stateRoot: "0xb74b556fe45c57f00c159c7ccf3de787ad834aa4d1ef0592343ba0bef472d2dd",
    timestamp: 1628688736,
    totalDifficulty: 12071162,
    transactions: [],
    transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
    uncles: []
  }
  ```
  - `miner` 採掘したEOAのアドレス
  - `number` ブロック高、指定したブロック高と同一の値が表示されている
  - `hash` ブロック-ヘッダ情報をSHA-3アルゴリズム適用して得られた32-byteのハッシュ値
    - ブロックを指し示すユニークID
  - `parentHash` 親ブロックのブロック-ヘッダハッシュ、これによりブロックチェーンを形成する

- 報酬の確認(`wei`単位)
  ```sh
  > eth.getBalance(eth.accounts[0])
  180000000000000000000
  > eth.getBalance(eth.accounts[1])
  0
  ```

- 報酬の確認(`ether`単位)
  ```sh
  > web3.fromWei(eth.getBalance(eth.accounts[0]),"ether")
  180
  > web3.fromWei(eth.getBalance(eth.accounts[1]),"ether")
  0
  ```

### 送金する

- アカウントロック解除
  ```sh
  > personal.unlockAccount(eth.accounts[0])
  Unlock account 0x2d8296bfec1444d6b6d69ba15132f1441144747e
  Passphrase:
  true
  ```

- 送金
  ```sh
  > eth.sendTransaction({from: eth.accounts[0], to: eth.accounts[1], value: web3.toWei(5, "ether")})
  "0xa377c9aef16265ab90bf86c2ff97f9d71ee6f7260998209b0d556323df8c55bb"
  > eth.getBalance(eth.accounts[1])
  5000000000000000000
  ```
  - 送金の際は、採掘処理をバックグラウンドで実行しておく必要がある

### トランザクション手数料

- 送金
  ```sh
  > personal.unlockAccount(eth.accounts[1])
  Unlock account 0xe408c887841b62d92469fd3b3582e5174d770ae2
  Passphrase:
  true
  > eth.sendTransaction({from: eth.accounts[1], to: eth.accounts[0], value: web3.toWei(3, "ether")})
  "0xb595769a0a5b1a6777934d8bb6eaa939f1217853895ad5534e89e8570b6502c7"
  ```

- トランザクション手数料
  ```sh
  > web3.fromWei(web3.toWei(5 - 3, "ether") - eth.getBalance(eth.accounts[1]), "ether")
  "0.000021"
  ```

### トランザクション情報を確認する

- `eth.sendTransaction`で返却される値がトランザクションのID
  ```sh
  > eth.sendTransaction({from: eth.accounts[1], to: eth.accounts[0], value: web3.toWei(3, "ether")})
  "0xb595769a0a5b1a6777934d8bb6eaa939f1217853895ad5534e89e8570b6502c7"
  ```

- 確認する
  ```sh
  > eth.getTransaction("0xb595769a0a5b1a6777934d8bb6eaa939f1217853895ad5534e89e8570b6502c7")
  {
    blockHash: "0xd2f81c767dfa2690d96c183b5ba45f82a290234c0da9d3680f4e5fe652b94409",
    blockNumber: 544,
    from: "0xe408c887841b62d92469fd3b3582e5174d770ae2",
    gas: 21000,
    gasPrice: 1000000000,
    hash: "0xb595769a0a5b1a6777934d8bb6eaa939f1217853895ad5534e89e8570b6502c7",
    input: "0x",
    nonce: 0,
    r: "0x9823b588fad088da042983e3e76d37970966090dac5bb4b79723d4857dcb47b7",
    s: "0x22847b3edd6b9546374f614e5f2aa922be4f5f7a377c0c0c870e274f507d9136",
    to: "0x2d8296bfec1444d6b6d69ba15132f1441144747e",
    transactionIndex: 0,
    type: "0x0",
    v: "0x41",
    value: 3000000000000000000
  }
  ```
  - `blockHash` & `blockNumber` このトランザクションを含んだブロックのヘッダ-ハッシュとブロック高
    - 採掘されていないと空
  - `gas` トランザクションの処理時のgasの使用量の**最大値**(実際のトランザクション処理時の**の使用量ではない**)
    ```sh
    > // トランザクション手数料
    > var fee = web3.toWei(5 - 3, "ether") - eth.getBalance(eth.accounts[1])
    undefined
    > // トランザクション
    > var tx = eth.getTransaction("0xb595769a0a5b1a6777934d8bb6eaa939f1217853895ad5534e89e8570b6502c7")
    undefined
    > // トランザクションで実際に使用されたgas
    > fee / tx.gasPrice
    21000
    ```

## スマートコントラクト

- アカウントのContractのこと
- EOAからContractにトランザクションを生成する事ができる
- EOAに送金するときとは異なり、送金と同時にContractのコードを実行することができる
- コードの実行は採掘者によって行われる
- 実行結果はブロックチェーンに書き込まれる
- Ethereum Virtual Machine Code(EVM)という形式で記述されている
- SolidityはEVMにコンパイルされる専用言語
- solc => Solidityコンパイラ

### solcのインストール

<https://solidity-jp.readthedocs.io/ja/latest/installing-solidity.html>

```sh
$ npm install -g solc
$ solcjs --version
0.8.6+commit.11564f7e.Emscripten.clang
```

### スマートコントラクトの作成から実行までの流れ

1. コントラクトコードを作成
  - コントラクトコードを、solcを使ってコンパイルする

2. Contractアカウントを生成
  - コンパイル済みのコードをトランザクションに付加してネットワークに送信する
  - そのトランザクションを受信した採掘者は、トランザクションをブロックチェーンに登録する
  - これによりContractアカウントが生成されそのアドレスが発行される
3. スマートコントラクトへのアクセスと実行
  - スマートコントラクトを実行したいユーザーはContractアカウントへトランザクションを発行することによりスマートコントラクトを実行する

### スマートコントラクトを作成する

<https://docs.soliditylang.org/en/v0.8.4/introduction-to-smart-contracts.html#a-simple-smart-contract>

- コントラクトコード(ここでは`SingleNumRegister.sol`)を作成する
  - 複数のユーザーで１つの整数値を登録、更新するスマートコントラクト
  - `touch SingleNumRegister.sol`
  ```js
  // SPDX-License-Identifier: GPL-3.0
  pragma solidity >=0.4.16 <0.9.0;

  contract SingleNumRegister {
      uint storedData;

      function set(uint x) public {
          storedData = x;
      }

      function get() public view returns (uint) {
          return storedData;
      }
  }
  ```

- コンパイル
  ```sh
  $ solcjs --abi --bin SingleNumRegister.sol
  $ cat SingleNumRegister_sol_SingleNumRegister.abi
  [{"inputs":[],"name":"get","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"uint256","name":"x","type":"uint256"}],"name":"set","outputs":[],"stateMutability":"nonpayable","type":"function"}]
  $ cat SingleNumRegister_sol_SingleNumRegister.bin
  608060405234801561001057600080fd5b50610150806100206000396000f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c806360fe47b11461003b5780636d4ce63c14610057575b600080fd5b6100556004803603810190610050919061009d565b610075565b005b61005f61007f565b60405161006c91906100d9565b60405180910390f35b8060008190555050565b60008054905090565b60008135905061009781610103565b92915050565b6000602082840312156100b3576100b26100fe565b5b60006100c184828501610088565b91505092915050565b6100d3816100f4565b82525050565b60006020820190506100ee60008301846100ca565b92915050565b6000819050919050565b600080fd5b61010c816100f4565b811461011757600080fd5b5056fea2646970667358221220ddd55f69c457667aabd9c9d447efec6b756ab478771993b619de07113742b59c64736f6c63430008060033
  ```

- Contractアカウントを生成
  ```sh
  > var bin = "0x608060405234801561001057600080fd5b50610150806100206000396000f3fe608060405234801561001057600080fd5b50600436106100365760003560e01c806360fe47b11461003b5780636d4ce63c14610057575b600080fd5b6100556004803603810190610050919061009d565b610075565b005b61005f61007f565b60405161006c91906100d9565b60405180910390f35b8060008190555050565b60008054905090565b60008135905061009781610103565b92915050565b6000602082840312156100b3576100b26100fe565b5b60006100c184828501610088565b91505092915050565b6100d3816100f4565b82525050565b60006020820190506100ee60008301846100ca565b92915050565b6000819050919050565b600080fd5b61010c816100f4565b811461011757600080fd5b5056fea2646970667358221220ddd55f69c457667aabd9c9d447efec6b756ab478771993b619de07113742b59c64736f6c63430008060033"
  undefined
  > var abi = [{"inputs":[],"name":"get","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function"},{"inputs":[{"internalType":"uint256","name":"x","type":"uint256"}],"name":"set","outputs":[],"stateMutability":"nonpayable","type":"function"}]
  undefined
  > // Contract オブジェクトを生成
  > var contract = eth.contract(abi)
  undefined
  > // アンロックが必要
  > personal.unlockAccount(eth.accounts[0])
  Unlock account 0x2d8296bfec1444d6b6d69ba15132f1441144747e
  Passphrase:
  true
  > オブジェクト情報を含んだトランザクションをEthereumネットワークに送信
  > var myContract = contract.new({ from: eth.accounts[0], data: bin})
  undefined
  ```

- 採掘前のトランザクション
  ```sh
  > myContract
  {
    abi: [{
        constant: false,
        inputs: [{...}],
        name: "set",
        outputs: [],
        payable: false,
        stateMutability: "nonpayable",
        type: "function"
    }, {
        constant: true,
        inputs: [],
        name: "get",
        outputs: [{...}],
        payable: false,
        stateMutability: "view",
        type: "function"
    }],
    address: undefined,
    transactionHash: "0x3e2e4780757671e750ca282f67ffd1c88ba8a6e871cb890ed8bc2d3474abb790",
    allEvents: function(),
    get: function(),
    set: function()
  }
  ```

- 採掘後のトランザクション
  ```sh
  > myContract
  {
    abi: [{
        inputs: [],
        name: "get",
        outputs: [{...}],
        stateMutability: "view",
        type: "function"
    }, {
        inputs: [{...}],
        name: "set",
        outputs: [],
        stateMutability: "nonpayable",
        type: "function"
    }],
    address: "0xe3e8f4876d321219ccfd7a34a6cf7da69927600f",
    transactionHash: "0x3e2e4780757671e750ca282f67ffd1c88ba8a6e871cb890ed8bc2d3474abb790",
    allEvents: function(),
    get: function(),
    set: function()
  }
  ```
  - `address`がコントラクトのアドレスとして登録された

- Contract ABI(Application Binary Interface)
  ```sh
  > myContract.abi
  [{
      inputs: [],
      name: "get",
      outputs: [{
          internalType: "uint256",
          name: "",
          type: "uint256"
      }],
      stateMutability: "view",
      type: "function"
  }, {
      inputs: [{
          internalType: "uint256",
          name: "x",
          type: "uint256"
      }],
      name: "set",
      outputs: [],
      stateMutability: "nonpayable",
      type: "function"
  }]
  ```

- ContractアカウントのアドレスとABIのを使用してスマートコントラクトにアクセス
  ```sh
  // コントラクトのオブジェクトを取得
  > var cnt = eth.contract(myContract.abi).at(myContract.address);
  ```

- トランザクションを生成して、コントラクトのコードを実行する
  ```sh
  // アンロック
  > personal.unlockAccount(eth.accounts[0])
  Unlock account 0x2d8296bfec1444d6b6d69ba15132f1441144747e
  Passphrase:
  true
  // コントラクトのsetを使用して登録値を6にする
  > cnt.set.sendTransaction(6,{from:eth.accounts[0]})
  "0xe40b9beda9f9eea5b1796a12a6f2a53203d7cd7a03202c543ac6b8dae873a8e7"
  ```

- Contractの状態を参照する
  ```sh
  > cnt.get.call()
  6
  ```

## メインネット(Mainnet)に接続する

- 接続
  ```sh
  geth --datadir mainnet_data console 2>> mainnet_data/e01.log
  ```
- 接続状況を確認する
  ```sh
  > net.peerCount
  3
  > admin.peers
  [{
      ...
  }, {
      ...
  }, {
      ...
  }]
  ```

- 採掘を始めるとまずメインネットでのブロックチェーン情報との同期が始まり、数日かかる
- ブロックチェーン情報は数ギガバイトのオーダーの容量
- `--data-dir`オプションで指定するディレクトリは容量の大きい領域内のディレクトリを指定すること
