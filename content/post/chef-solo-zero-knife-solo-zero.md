+++
date = "2014-12-25T00:00:00+09:00"
draft = false
slug = "chef-solo-zero-knife-solo-zero"
tags = ["chef", "knife-zero"]
title = "Chef-Solo, Chef-Client LocalMode, Knife-Solo, Knife-Zero and us."

+++

> ※旧ブログから移植。

Chef-Soloが無くなるので、他の何かに乗り換える必要があるのか。 という話に対してKnife-Zeroをダシに何か言っておこう。

最初に言っておくと、今Chef-Solo、Knife-Soloを使っている人、多分何もしなくていい。


## 登場人物一覧

- Chef-Solo
    - Chef-Server無しでローカルサーバにChefのレシピを適用する。Chef-Server非互換(11でほぼ改善)。
- Chef-Client LocalMode
    - Chef-Server無しでローカルサーバにChefのレシピを適用する。Chef-Server互換。
- Knife-Solo
    - レシピ一式を転送し、Chef-Soloをリモートサーバに適用する。
    - (追記)全体のワークフローは、VagrantのProvisionerに近い。
- Knife-Zero
    - レシピ一式を転送せず、SSH越しにChef-Client LocalModeを実行する。
    - (追記)従来のClient／Serverワークフローを踏襲。


## 全てのChef-Solo関連がにChef-Zero(Chef-Client LocalMode)に変更されるというのは飛躍的な判断

なんとなく騒がれているのは、これの元記事からなのかしら。

- [[和訳] ソロからゼロへ: Chef Clientローカルモードへの移行 #opschefja #getchefja « CREATIONLINE, INC.](http://www.creationline.com/lab/6085)

基本的にこれはサーバのローカルで実行する場合についての話でしょう、まあそれならChef-Client LocalModeに変えるのはほとんど何も変更はいらないのでよくわかります。

> 参考： Chef-RFC031 『This means that chef-solo using "local mode" must be 100% backwards-compatible with existing chef-solo usage.』

で、リモートでやるKnife-Soloについて考えた時に、"Chef-Solo無くなる"＝"Knife-Solo使えなくなる"でもないわけです。 Knife-Soloは単なる非公式のプラグインであって、公式がこういう記事でいちいち言及しないでしょうね。


### 少なくともKnife-SoloはChef-Soloと心中なんてしないだろう

日本でKnife-Soloがそこそこ広い認知度を持っているのは、[伊藤直也さんの入門Chef Solo - Infrastructure as Code](http://www.amazon.co.jp/dp/B00BSPH158)によるところが大きいでしょう。多分そう、部分的にそう。

この本で覚えたフローや使い方が今後できなくなるかって言うと多分そんなことはなくて。 Knife-Solo開発陣はChefの動向をちゃんと追っています。

[add local_mode to solo.rb · Issue #353 · matschaffer/knife-solo](https://github.com/matschaffer/knife-solo/issues/353)

(関連ディレクトリをまるまる転送、リモートでLocalMode相当の実行形式に変更など)

ということでKnife-Soloでやっているものは、基本的にそのまま使い続けることもできそうだと思ってます。 実際Chef-Soloって無くなるのかも不明、もしかしたらChef14とか15になれば居なくなるのかもしれませんね。


## Knife-ZeroはミニマムなChef-Server環境とのアップ/ダウングレード移行パス


じゃあKnife-Zeroはどういう位置かというと、Chef-Server環境との相互移行がやりやすいChefの導入方法(の一つ)です。 SSH越しにリモートで実行されるChef-ClientはLocalModeではなく、通常のClientモードとしてChef-Server(SSH元のChef-Zero)に対して接続します。

なのでKnife-Solo(そもそも私は使ってない)の代わりではなく、既存のChef-Serverを畳む用途で作りました。

- Knife-Zeroではじめて、Node数が増えてきたらChef-Serverへ
- Node数が少ないChef-ServerをたたんでKnife-Zeroへ

Knife-Zeroは、概ねお一人様用Chef-Serverという感じです。node情報ををgit管理できたり、エディタで手元からしれっとattributeを追加できたりという利点とかありますが。 大人数で保守する場合や、継続的にChef-Clientを実行したい場合、Nodeがとても多い場合は多分Chef-Serverのほうがよいのかと思います。

## Knife-Zero豆知識

- Chef-Zeroは2,000nodeくらいまでなら性能的に問題ない。 ([Chef-Zeroのキャパシティが気になったのでRubyでベンチマークをとった - Qiita](http://qiita.com/sawanoboly/items/35dd2f117262f4f21969))
- Knife-Zero制作のきっかけはlightchef([現itamae(github link)](https://github.com/ryotarai/itamae))
    - Severspec的発想がいいと思った。
    - しかし同時に、『インベントリも収集せずに(light)Chefとはどういう了見か』と思った。
- Facebookの中の人も、SSH越しのLocalMode(Chef-Zero)を使っている。
    - 手抜きのために考える事は同じだなと思いました。
    - [SSL support · Issue #86 · opscode/chef-zero](https://github.com/chef/chef-zero/issues/86)
- 最初は本家へのPull Requestとして作成していた
    - とても煩雑なコードになって、説明するのが面倒に。
    - とりあえずリリースを優先、楽なKnifeのプラグインとして作成。
    - 本家にマージしたい。(最近はそうでもない。)
    - Chef本体からあまりコードの量的に離れたくない。
- リリース当初の私が書いたコードはほとんど残っていない。
    - [@yasushia](https://twitter.com/yasushia)氏によってリファクタリングが行われ、とてもスッキリしたのでv1.0.0を付けた。
- (Fixed) Chef12のRC版ではローカルモードが盛大にクラッシュする。
    - [The localmode doesn't create node object correctly on chef 12. · Issue #2433 · opscode/chef](https://github.com/opscode/chef/issues/2433)
    - Knife-Zeroは自分では便利なので12対応はしておきたいんだけど。
    - *羅列したこの記事のタイトルで、一番寿命が先に来るのは、もしかしてKnife-Zeroかもしれないよ。*
    - (New) 治した！ [use Chef::JSONCompat.parse for filecontents #2433 by sawanoboly · Pull Request #2482 · opscode/chef](https://github.com/chef/chef/pull/2482)

つらつらと書きましたが、Knife-Zeroは色々と楽をできるように作ったので、新規のシステムをChefで作り始めるとか、既存のシステムにChefを導入するとかならKnife-Zeroをおすすめします。
