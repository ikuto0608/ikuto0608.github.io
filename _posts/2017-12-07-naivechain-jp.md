---
layout:     post
title:      最小コードでBlockchainを表現する
date:       2017-12-07 19:19:00
summary:    Naivechainを参考にRubyでかいてみる
categories: blockchain programming
image: images/movie.jpg
---

ブロックチェーンて？Bitcoinて？具体的にはどんなものかというのが複雑でいまいち把握できずにいたところ、この[naivechain(https://github.com/lhartikk/naivechain)](https://github.com/lhartikk/naivechain)というrepositoryに出会ったので写経しつつ理解しようと思いました。ここでは最小限の機能を持ったブロックチェーンを最小行のソースコードで表現しているのでブロックチェーンの基本的な考えをやさしく理解できるのではないかなと思います。同じように[naivechain](https://github.com/lhartikk/naivechain)を参考に書き換えているrepositoryがGithubでたくさん見られたので負けじと僕もやっていこうかなと。Javascriptて書かれたものなのですがこれをRubyでかき換えつつ噛み砕いでいった過程を記します。

##### 登場人物
 - Node       --> まな板ですね、全ての作業はここで行われる
 - Mining     --> 包丁ですね、絶えずシャキンシャキン動いています
 - P2P        --> 菜箸ですね、まな板から他のまな板へ材料を移動させる感じ
 - Block      --> 素材です、ウインナーです
 - Blockchain --> 腸詰です、ウインナーが連なってます
 - Hash       --> 秘密のスパイスです

といったようにあまり数が多くないので本当にシンプルに事は進みます。

---

#### 1, 仕込み

まな板を準備します、複数まな板があることを前提として、腸詰を上手にシェアするために `port` を教えあいます。菜箸こと `TCPSocket` を使ってP2Pを実現します。 `TCPSocket` と `TCPServer` の関係が正直なところ最初 `？` だったんですが、 `TCPServer` はひたすら `TCPSocket` が来るのを待つんですよね、待って待ってついに `TCPSocket` がきたら他の `TCPSocket` をあてがってあげて、また新たな `TCPSocket` が来るのを待つんです。待つんです。掴んでいるのは腸詰です。

```

  class Node
    def initialize(port, other_node_ports = [])
      @clients = []
      other_node_ports.each do |other_node_port|
        client = TCPSocket.open('localhost', other_node_port)
        Thread.start do
          listen_message(client)
        end

        @clients << client
      end

      @server = TCPServer.open('localhost', port)
      init_connection
    end
  end

```

こんな感じに `initialize` はなりまして、すでに存在する `TCPSocket` のportに通信を試みる役割と、あとで `TCPSocket` が通信して来る時の備えの役割が主ですね。

---

#### 2, 包丁さばき

舞台はまな板の上で整ったので具材を切っていきしょう。ブロックチェーンで重要なキーワード `mining` と呼ばれる処理がここになりますね。 `mining` を行って秘密のスパイスを生成します、これを加えたものがウインナーになります。秘密のスパイスを作るのに時間をかけるとその分ウインナーが美味しくなるのは自明ですね、その時間をかける工程によってBitcoinは信頼性を保っていると理解しています。ここではあまり時間をかけられないので化学調味料で代替します。味の素です。

```

  def mine_with_loop
    Thread.start do
      loop { mine; sleep(Random.rand(10)) }
    end
  end

  def mine
    @blockchain << generate_next_block(@blockchain.last)
    broadcast
  end

```

`mining` をエンドレスに行います、行いますが容易に変化がみれるように1シャキンあたり0〜10秒待ってシャキンシャキンします。
一振りで一つのウインナーが作られます。

```

  def generate_next_block(block_data)
    previous_block = @blockchain.last
    next_index = previous_block["index"] + 1
    next_timestamp = Time.now.to_i
    next_data = "Smart contract" + next_index.to_s
    next_hash = calculate_hash(
      next_index,
      previous_block["hash"],
      next_timestamp,
      next_data
    )

    {
      "index" => next_index,
      "previous_hash" => previous_block["hash"],
      "timestamp" => next_timestamp,
      "data" => next_data,
      "hash" => next_hash
    }
  end

```

`block` に含まれているのは
 - `index` --> 何番目のブロックか
 - `previous_hash` --> 一つ前の鍵
 - `timestamp` --> 作れた時刻
 - `data` --> データ
 - `hash` --> 正しさを示す鍵となるもの

から構成されています。

---

#### 3, 仕上げ

ここで作ったウインナーを腸詰に繋げてみんなに自慢するために他のまな板へ投げつけます、使うのは菜箸です。これを受けとった他のまな板は、この腸詰の完成度をひとつひとつ入念に調べます。完成度が満足していてかつ腸詰の長さ(ウインナーの数)がおおきければ自分が作っていた腸詰は捨てこれを採用します。出来が今一つの場合、これを投げ捨てます。どちらにせよそこからまた包丁をシャキンシャキンしていきます。

```

  def verify(blockchain)
    replace_chain(blockchain) if is_valid_chain(blockchain)
  end

  def is_valid_chain(blockchain)
    if blockchain[0] != get_genesis_block
      return false
    end
    temp_blocks = [blockchain[0]]
    blockchain[1..-1].each_with_index do |block, index|
      if is_valid_new_block(block, temp_blocks[index])
        temp_blocks << block
      else
        return false
      end
    end

    true
  end

  def is_valid_new_block(new_block, previous_block)
    if previous_block["index"] + 1 != new_block["index"]
      return false
    elsif previous_block["hash"] != new_block["previous_hash"]
      return false
    elsif calculate_hash_for_block(new_block) != new_block["hash"]
      return false
    end

    true
  end

```

---

#### 4, 秘密のスパイスの作り方

秘伝の業でなされるものなので、過去のウインナーを参考にしつつ新しい血を加え、特定のアルゴリズム(ここではbase64digest)を用いて文字列に変えます。この値の確かさをのちに確かめる必要があるのでこの変換結果は普遍でないといけないですね。ここのhashの生成方法過程が実用されているコインではきっと面白難しいはずなので追々もっと深掘りしていこうと思います。
ここでは簡易的に味の素を使用します。

```

  def calculate_hash_for_block(block)
    calculate_hash(block["index"], block["previous_hash"], block["timestamp"], block["data"])
  end

  def calculate_hash(index, previous_hash, timestamp, data)
    Digest::SHA256.base64digest(index.to_s + previous_hash.to_s + timestamp.to_s + data.to_s).to_s
  end

```

---
#### 終わりに

といった風に以上の工程を繰り返し繰り返し行い、ブロックチェーンは巨大化していくのですね。これをはじめとして
 - hash生成のアルゴリズム
 - dataの存在の仕方の詳細、各々コインでどんなdataが入っているのか、多分そこが色々あるコインの特徴となるところなんじゃないの？
 - 本当に各Nodeがすべてのブロックチェーンを保持しているの？それってものすごく長くない？

などなど疑問は山積みなのでひとつひとつ神砕ければなと思います。

[コードはここに https://github.com/ikuto0608/naivechain](https://github.com/ikuto0608/naivechain)
