---
layout:     post
title:      Dappプログラマへの最初の一歩
date:       2017-12-18 01:06:00
summary:    プラットフォームとしてのEthereum
categories: blockchain Ethereum dapp
image: images/bitcoin.jpg
---

こちらは[Frog Advent Calendar 2017 (https://adventar.org/calendars/2350)](https://adventar.org/calendars/2350)の19日目に載っています。


![bitcoin]({{ site.url }}images/bitcoin.jpg)


#### はじめに

ここ一年のCryptocurrencyの盛り上がり具合は目を見張るものがあります。惜しくもこの波に乗ることはできずミリオネアになることは叶わなかったのですが、Teck業界に従事するものとして知識としてこれらの技術を学ぶのは大切なのではないかなと、まだ遅くはないことを祈ってぐぐっていきたいと思います。

---

#### Dappって?

Dappとは `decentralized app` の略でBlockchainをベースとする、かつ従来の、データベースをひとつのところで管理しないアプリケーションを指すところが広義の意味だと思うのですが実際にはEthereumをプラットフォームとする派生のアプリケーションをDappと言っているように思います。代表的なところでは

 - [Storj](https://storj.io/) --> Cloud storage
 - [Gnosis](https://gnosis.pm/) --> Prediction market platform
 - [Decentraland](https://decentraland.org/) --> A virtual world owned by creators, powered by economic opportunity
 - [Golem](https://golem.network/) --> Distributed computation

などが挙げられます。上記のアプリケーションは[https://www.stateofthedapps.com/](https://www.stateofthedapps.com/)の一覧で見られ、かつ[https://coinmarketcap.com/](https://coinmarketcap.com/)で上位に位置するものをピックアップしました。あとはつい最近話題に上がった[cryptokitties](https://www.cryptokitties.co/)、こちらもEthereumを使ったDappですね、あまりにもTransactionに負荷をかけたことから話題を呼んでいましたね[https://cryptovest.com/news/ethereum-loses-to-cryptokitties-network-remains-slow/](https://cryptovest.com/news/ethereum-loses-to-cryptokitties-network-remains-slow/)。こちらは嬉しいことにバンクーバーを拠点とする企業が作ったもので親近感がいっそう湧きました[axiomzen](https://www.axiomzen.co/)。(就活中にこの企業に応募しましたが返事は返って来ず。残念)


---

#### なんでEthereumなのか

decentralizedなアプリケーションを一から作るのは敷居が高いのですが、この敷居を下げてくれたのがEthereum。イメージとしてはすでにもう存在するEthereumネットワークを間借りする形でそれぞれDappを開発できる。それぞれの `Contract` が `Ethereum` の `Transaction` に積まれていく、ただし個々の `Contract` はそれぞれアプリで固有なので呼ばれた先で振る舞いがそれぞれ違う。そんな形でいろんなDappがEthereumネットワークを共有している、というのがふわっとした私の理解です。

---

#### どうやってDapp作るの？

このチュートリアルブログ[(https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-1-40d2d0d807c2)](https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-1-40d2d0d807c2)を参考に投票アプリをプログラミングしていきます。開発する上でキーとなるライブラリ/アプリケーションはこちら
- [geth](https://github.com/ethereum/go-ethereum/wiki/geth) --> ローカルにEthereumのNodeを立ち上げるsoftware
- [truffle](http://truffleframework.com/) --> 自分で作ったcontractをコンパイル/デプロイするフレームワーク
- [web3js](https://github.com/ethereum/web3.js/) --> クライアントでEthereumと交信するためのライブラリ


これらを使いこなすことがDappプログラマの条件なんじゃないかなと。
とはいえ、実際のところ私はまだこの[チュートリアル Part2](https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-2-30b3d335aa1f)で止まっているのですが、、なぜか？


---

#### 環境構築って大事

どこで今つまづいているか、Dappを作る上で欠かせないのはEthereumですね。Ethereumには二つのパプリックネットワークが存在していて `Mainnet` (通称 `Homestead` )と `Testnet` (通称 `Ropsten`)があります。本番用のネットワークとテスト用のネットワークですね。なので開発途中は `Ropsten` に接続して実際に動作をチェックできるようになっているのですね、便利!で私は今 `Ropsten` に接続するところで詰まっていまして、、初めてネットワークに接続する場合には、存在するBlockchainをまず全てローカルに落とさなきゃいけないのですがそれが終わらない、数GBあるものなので時間がかかることは明記されているんですが、P2Pが全然繋がらず、繋がってもFailedする始末、困ったなと。

![peer failed]({{ site.url }}images/peer_failed.png)

およ、といいつつももう少し待っていたら
```

  truffle(development)> web3.eth.syncing
  { currentBlock: 151872,
    highestBlock: 2287008,
    knownStates: 1,
    pulledStates: 0,
    startingBlock: 77312 }

```
となり最新の[ブロック情報 (https://ropsten.etherscan.io/block/2287104)](https://ropsten.etherscan.io/block/2287104)と比べると値が近いのでこれはやっとスタート地点のたったのではないかなと。
いやまだでした
```

  truffle(development)> web3.eth.getBlock("latest").number
  0

```
あれれ、最新の値が取れない、syncが正常にできていないのか。それが証拠に
```

  truffle(development)> web3.eth.getBalance('0x2bd86de9be88fedffccd5fe6f6777dfe51bc00d2')
  { [String: '0'] s: 1, e: 0, c: [ 0 ] }

```
自分のアドレスに正しい `Ether` の値が表示されない。本来なら所持している `5 Ether`  [(https://ropsten.etherscan.io/address/0x2bd86de9be88fedffccd5fe6f6777dfe51bc00d2)](https://ropsten.etherscan.io/address/0x2bd86de9be88fedffccd5fe6f6777dfe51bc00d2)表示されるはずなんですが。ちなみにこれは自分で `mine` したわけではなく、[reddit](https://www.reddit.com/r/ethdev/comments/72ltwj/the_new_if_you_need_some_ropsten_testnet_ethers/)で自分のアドレスを投稿すると誰かが送ってくれます。 `5 Ether` は今の相場ではUS$1,400-くらいですね、テスト用なんで無価値ですが。

---

#### 終わりに

全然終わっていないのですが、諸事情により本日はここまでとさせていただきます。次回にはチュートリアル完結版をあげれるように励みます。

救いの一手をお持ちの方はご一報いただけると。
`「聞くはいっときの恥、知らぬは一生の恥」` 祖母が口すっぱく言う言葉より
