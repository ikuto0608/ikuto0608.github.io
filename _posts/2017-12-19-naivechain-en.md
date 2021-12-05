---
layout:     post
title:      It describes Blockchain with minimum line.
date:       2017-12-19 19:19:00
summary:    I'm going to write Blockchain in Ruby, inspired by Naivechain
categories: blockchain programming
image: images/coin_news.png
---

![coin news]({{ site.url }}images/coin_news.png)

What is Blockchain? Bitcoin? I couldn't understand it lately but there was a cool repository which is [naivechain(https://github.com/lhartikk/naivechain)](https://github.com/lhartikk/naivechain), so now I'd love to code and understand it! This repo represents what basic knowledge of Blockchain is with a small amount of codes in Javascript. It's not fun to just copy the code so I'm going to code this in Ruby here. It seems a lot of people are doing the same thing, so I'm not going to be shy and just do it for me!


##### ELEMENTS

 - Node       --> Cutting board; everything happens on here
 - Mining     --> Knife; Cut, cut, cut, cut, and cut
 - P2P        --> Cooking chopsticks; This lets you move ingredients to a cutting board from other cutting boards
 - Block      --> Ingredient; sausage
 - Blockchain --> Sausages; Sausages connect
 - Hash       --> Spice; Secret spice

As you can see, it's not complex; there's only a few elements here.

---

#### 1, PREPARATION


Let's put the cutting board first. Let's assume there are multiple cutting boards so that we need to communicate `port` between them to be able to share the sausages smoothly. It is used by `TCPSocket` also known as cooking chopsticks to build it. I couldn't understand the relation between `TCPSocket` and `TCPServer` at first, but `TCPServer` waits for `TCPSocket`. It waits, waits and waits for coming `TCPSocket` and if `TCPSocket` comes at last, then `TCPServer` waits for another `TCPSocket`. This is grabbing sausages.

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

`initialize` is gonna be like this. It tries to connect to the existing `TCPSocket` and prepare to wait for other `TCPSocket`.

---

#### 2, HANDLING A KNIFE

Everything is on the stage - the cutting board - so then let's cut the ingredients! This logic shows you one of the important keywords of Blockchain which is `mining`. We do `mining` to make the secret spice, and the secret spice is used to make sausage. The more time you take to make the secret spice, the more tasty your sausages will be. That's a fact, isn't it? In my understanding, taking much time guarantees the correctness of Blockchain. But I cannot take much time here so I'm gonna use chemical seasoning. It's called `Ajinomoto`.


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

This code does `mining` endlessly. Simplified, it cuts every 0 to 10 seconds. One cut makes one sausage.

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

`block` includes
 - `index` --> which index of block
 - `previous_hash` --> previous hash key
 - `timestamp` --> created at
 - `data` --> data
 - `hash` --> proof key


---

#### 3, FINISH


Finally, you're going to toss your sausages to people to show them how good your sausages are! You use cooking chopsticks for this part. People who receive your sausages evaluate how your sausages' tastiness. If they're delicious and the length of sausages is longer than the person's own, then the person is going to keep yours but if it's not, he is gonna throw it away. And then the person will start to cut again.


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

#### 4, HOW TO MAKE THE SECRET SPICE


This comes from a secret formula so that it inherits old sausages and adds a new generation. Here we use `base64digest` to calculate the string. It's supposed to be an unchanging calculation in order to confirm correctness every time. How to generate hash for real is more difficult than this. I'll study it another time - here we use simple way:


```

  def calculate_hash_for_block(block)
    calculate_hash(block["index"], block["previous_hash"], block["timestamp"], block["data"])
  end

  def calculate_hash(index, previous_hash, timestamp, data)
    Digest::SHA256.base64digest(index.to_s + previous_hash.to_s + timestamp.to_s + data.to_s).to_s
  end

```

---
#### END


As we repeat these process Blockchain is getting bigger and bigger. As a starting point here, I'm going to understand:
 - the algorithm of generating hash
 - how data exists and how the data exists differently between altcoins (I believe this is why there are many types of altcoins)

etc..... I still have a ton of questions for Blockchain, so I'd love to figure them out one by one.


[Here is the source code https://github.com/ikuto0608/naivechain](https://github.com/ikuto0608/naivechain)
