+++
date = "2014-02-16T00:00:00+09:00"
draft = false
slug = "xegg-sakura-with-chef"
tags = ["chef","sakura_internet","event"]
title = "第一回XEggで『さくらのクラウドフォーメーション with Chef 』"

+++

2月15日に大阪のグランフロントで開催された、[Innovation EGG 第二回 XEgg 1st『クラウド未経験者向けITコミュニティ＆クラウドベンダー合同勉強会』](http://innovationegg.doorkeeper.jp/events/7435)で行ったセッションの話。

- [Innovation EGGとは？](http://innovationegg.doorkeeper.jp/)

## イベントのテーマと課題

今回声がかかったのはXEgg(クロスエッグ)と題されたイベントで、テーマによって選出されたベンダにITコミュニティを組み合わせてなんかやってくれと言う話だった。

第一回XEggのテーマはクラウド、私は[Chef meetup Kansai](http://chef-meetup-kansai.doorkeeper.jp/)の主催者としてITコミュニティ枠で参加。 さわる対象のクラウドベンダは『さくらインターネット』、要は『さくらのクラウド』を触りましょうと。

<iframe src="//www.slideshare.net/slideshow/embed_code/key/oTufMIf9dcRFMa" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/YukihikoSawanobori/x-egg01-chef" title="さくらのクラウドフォーメーション with Chef [XEgg session]" target="_blank">さくらのクラウドフォーメーション with Chef [XEgg session]</a> </strong> from <strong><a href="//www.slideshare.net/YukihikoSawanobori" target="_blank">Yukihiko SAWANOBORI</a></strong> </div>

## セッション裏話

セッションの大筋はスライドの通りで、話した内容はその場で聞いていただいた方々がご存知です。 ブログではどうしてあんなセッションになったのか振り返ってみます。

### ターゲットの決定

イベントの参加対象者は、『クラウド未経験者』とのことでした。ずいぶんもやっとしています。

セッションの内容をChef初心者向けに丁寧に解説とすることも考えましたが、テーマからいって参加者がChefに興味あるとも限らないうえ、同時に動いていたプロジェクトのこともあわせて、そういうのは 私が飽きていました 。

そもそも私はChefを売ってるわけでもないし、イベントの主軸はクラウドベンダだし、どうせなら自分が興味あることをセッションにしてしまうほうが楽しい。

ということでセッションのターゲットはクラウドベンダの『さくらインターネットのスタッフ』と、同枠の『MongoDBコミュニティ』に絞りました。

### Chefの紹介

Chefはその特性上適用できる範囲がとても広く、組織によって様々に使い方を選べます。 そもそもChefには決まった使い方も無く、あるのは各組織のワークフローやポリシーに合わせてシステムの構成管理をできる柔軟性くらいです。

説明したってきりがないので、旧態依然とした構成管理への皮肉を交えつつ、最初から視聴者全員置いてきぼりを目標にさっと流しました。

イベントテーマ的に主役では無いですし。

### 使ってみました系と海外との比較

このへんは裏表あまりなく、結構思うまま言ったらこうなった。

<blockquote class="twitter-tweet" lang="ja"><p lang="ja" dir="ltr">sawanoboly氏、さくらの田中さんの前でさくらのクラウドAPIのdisを繰り広げている。でもこういうのは参考意見として良いよね。 <a href="https://twitter.com/hashtag/xegg?src=hash">#xegg</a></p>&mdash; MATSUMOTO, Ryosuke (@matsumotory) <a href="https://twitter.com/matsumotory/status/434508042814513152">2014, 2月 15</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

コンピュータリソースに特化したサービスと比べるのは少々ごまかしを含みますが、ややインパクトを重視してChefと手っ取り早く連携させたりとりあえずサービスをリリースしようといった視点からあえて見た体で展開することにしました。

実際、『ボクのデータセンター』を作りたい方には非常にいいと思います。

ここの中で『ホビー対傭兵』という比喩が出てきますが、この表現にもうちょっとだけ突っ込んでみます。

日本の企業は上流工程をプロパーが行い、開発や現場やを外注に任せるという図式がよく見られます。 自社好みにカスタマイズ可能なプロパーと、プロとして現場の取り仕切りを任せる外注さんと。スライドの傭兵さんはいわば外注に当たるわけですね。

国内ベンダと海外ベンダで提供するサービスと需要とに、なんだかちょっとねじれがあるような気がしています。

### Fogの『さくらのクラウド』サポートについて

以前から国内クラウド向けにはChefに関連したライブラリ([knife-zcloudjp](http://rubygems.org/gems/knife-zcloudjp),[kitchen-zcloudjp](http://rubygems.org/gems/kitchen-zcloudjp)など)を公開しており、機会があればFogのプロバイダも書いてみたいと思っていました。

そもそもFogに日本のプロバイダがないのは前々から気に入らなかったこともあって、今回の『さくらのクラウド』はそこそこ渡りに船でした。  
日本語がわからないとサインアップが困難だったりするのですが、そこは気にしないことにします。

このFog対応に思ったより時間をとられたので、他のやりたかったことができなかった弊害はありましたが、コンピュータリソースがカジュアルに上がってこないと(Chef的に)使いづらかったことは確かなので仕方がありませんでした。

### Chefとインテグレーション

スタートアップスクリプトのような使い捨てでなく、クラウドAPIを叩く瞬間からChefの支配は始まっているんですよというアピールをしてみました。 せいぜい計算リソースにすぎないコンピュータインスタンスに、たとえばわざわざ名前をつけて可愛がりながら育てるなんて、もはやナンセンスではないのかという啓発も込めています。

## まとめと今後の展開

言いたい放題のセッションでしたが、定めたターゲットさくらインターネットのスタッフ一同には田中社長をはじめ『面白かった』と感想をいただいたのでめでたしめでたしと。

しかし今回時間の関係上、『さくらのクラウド』の全力を引き出したとは言いがたい。  
そしてクラウドフォーメーションと銘打っておきながら、ネットワーク関係やLB、それと先日発表されたクラウドストレージにも手を出していません。

できればyamlかjsonの定義ファイルを食わせればシステム構築するところまで、それこそ某クラウドフォーメーション程度の実装までしておきたいなあ。
