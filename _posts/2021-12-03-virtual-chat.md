---
layout:     post
title:      チームでボールを投げあうに至った話
date:       2021-12-05 00:00:00
summary:    Shopify Partyなるものを見かけて感激したところからはじまった
categories: unity shopify
image: images/slack_notification.png
---

従業員が数百人いてかつリモートワークで日々仕事していると普段関わりのない同僚はほんとうに関わらないまま時間は過ぎて少しさみしいなと思っていたところにTwitterに流れてきたtweetがこれ。

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">We built an internal game at Shopify to make virtual hangouts more fun. It’s called Shopify Party, and I’d love to tell you all about it 👇🏻 <br>[1/6] <a href="https://t.co/DwzxwWty3V">pic.twitter.com/DwzxwWty3V</a></p>&mdash; Daniel Beauchamp (@pushmatrix) <a href="https://twitter.com/pushmatrix/status/1442839002432802818?ref_src=twsrc%5Etfw">September 28, 2021</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<br />

Shopify Partyとはなんぞ？Mario Partyから文字ってるものだと思っていて、Shopify社内でインターナルに公開してるゲームで同僚と一緒になっていろんなミニゲームができるものですと、ちょっとしたアイスブレイクに、スタンドアップの合間に遊びつつ、とっても楽しそうに映りました。

(やってみたい！公開してくれないのかShopifyー。)

これこれこれこれこういうのがあるとリモートでもコミュニケーションパスが増えてよさそう！と思ったので詳しくみていきました。

使われている技術はUnityとマルチプレイヤーを容易に実装できるサービス[normcore.io](https://normcore.io/)を使っていて、WebGL buildでブラウザ上で遊べてしまうと。お手軽！(WebGLはPrivateプランでのみ有効でした)

ということで、ここでは実際に[normcore.io](https://normcore.io/)を使ってものすごく簡単にマルチプレイヤー実現できちゃうんじゃんと驚いたことを記していきます。

--------

まずはSign upから、free planが用意されているので気軽に試すことができました。

![normcore plan]({{ site.url }}images/normcore-plan.png)


[チュートリアル](https://normcore.io/documentation/guides/creating-a-player-controller.html)があったので最初にこれを写経して雰囲気を掴みました。プレイヤーの移動ロジックやカメラ移動の実装はあとでごっそり参考にしました。

このチュートリアルにはなかったんですが、[normcore.io](https://normcore.io/)はボイスチャットにも対応していて話したい欲求が強くなってきました。会社にはカシュと呼ばれるカルチャーがあって、業後にフリースペースにおのおのそれぞれまばらに集まってフリードリンクを嗜むというものでコロナ前の頃ではよかったんですがいまや家から働くのが普通になってしまったので参加することができず.... これをバーチャルの世界で実現できたら楽しそう！をモチベーションに進めていきました。ゲームの中で自由に歩き回って人の輪を作れておのおのグループになってわいわいできたらいいなを目標にして。

便利なものでUnity Asset Storeにはなんでも揃っているんですね！フィールドにはオフィスを、プレイヤーにはヒトを再現したくていろいろあさったところ

いい感じのオフィスのアセット：[https://assetstore.unity.com/packages/3d/environments/urban/hq-archviz-loft-office-modular-186002](https://assetstore.unity.com/packages/3d/environments/urban/hq-archviz-loft-office-modular-186002)

歩くなどのアニメーションも付いているタイツマン：[https://assetstore.unity.com/packages/3d/characters/stickman-character-prototype-182000](https://assetstore.unity.com/packages/3d/characters/stickman-character-prototype-182000)

などなどよさそうに思ったので課金しました。

アセットを落としてきてそこからせっせかせっせとそれらしき空間に整えて、チュートリアルにもあったようにEmpty objectにPlayerManagerと名付けてRealtimeコンポーネントを追加しました。ここでApp Keyを[normcore.io](https://normcore.io/)から生成して持ってくる必要がありました。

![PlayerManager]({{ site.url }}images/player-manager.png)

プレイヤーとしたいタイツマンprefabにはRealtime View/Realtime Transform、それからボイスチャットを可能にするためRealtime Avatar Voiceコンポーネントを追加しました。追加するだけマルチプレイヤーになってしまうすごい。。

![Player]({{ site.url }}images/player.png)

だいたい動くことがみれたので、実際に試したい欲が高まり休日に遊んで書いていたものだったんですがチームの雑談時間を利用してチームメンバーに入ってもらって雑談をこの中でできないかテストをお願いしました。

![Preview1]({{ site.url }}images/preview1.png)

一度目、、惨敗。。。会話ができない。。ヒトの声が聞こえる距離を見誤りめっちゃ至近距離でないと声が聞こえませんでした。あと、タイツマン誰が誰だかわかんない問題。みんなにZoomに入りもらい直して反省会をさせてもらったりしました。感謝

Realtime Avatar Voiceは内部でUnityのAudioSourceを参照してるので明示的に宣言することで自由に設定が変えられ、声が聞こえる範囲を自分のフィールドに合わせて調節しました。

![AudioSource]({{ site.url }}images/audio-source.png)

それに加え、名前を表示したり申し訳程度に遊べる機能を追加してまたテストをお願い。

<br />
<br />
<br />

二度目、、聞こえる！喋れる！！

![Preview2]({{ site.url }}images/preview2.png)

牛になれるオプションを用意したり、

![Preview3]({{ site.url }}images/preview3.png)

ボールを投げる機能をいれたところ話すより投げるほうで盛り上がり結果雑談をまったくせずひたすらボールを投げては当てて終わりました。

![cow]({{ site.url }}images/cow.gif)
(実際に投げ合う画を撮り忘れたのでそれらしいものを添付)

プレイヤーの位置情報はRealtimeコンポーネントを追加することで勝手にしてくれますが、アニメーションの状態の同期やニックネームの同期は数ステップで必要で、[RealtimeModel](https://normcore.io/documentation/room/realtimemodel.html#realtimemodel-realtimeproperty-attributes)スクリプトを差し込んでこれを実現できました。


ここまでで、簡単にマルチプレイヤーでボイスチャットができるアプリを作れることが見れました。Zoomなどと違って同じ空間に複数グループを無作為に作って会話ができるのがいいのかなと。話すだけでなくShopifyのようにいろんなミニゲームを仕込んでいくのもよし(大変そう)、metaverseの波に乗ってVRにするのもよし(もはやそれはVRchatかな)、なにがしの形になるように引き続き進めていきます！
