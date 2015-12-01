+++
date = "2015-12-01T14:17:10+09:00"
draft = false
slug = "tk-shell-verifier-merged-into-master"
tags = ["test-kitchen"]
title = "Test-KitchenのShell-Verifierがマージされた。"

+++

これでRubyGemsの[kitchen-verifier-shell](https://rubygems.org/gems/kitchen-verifier-shell "kitchen-verifier-shell | RubyGems.org | your community gem host")は御役御免となりそう。

ことの次第などはQiitaに昔書いちゃったのでそっちに。

- [Test-KitchenでServerspecやInfratasterをShell-Verifierから実行 - Qiita](http://qiita.com/sawanoboly/items/4898bb4f277e5cacfb6c "Test-KitchenでServerspecやInfratasterをShell-Verifierから実行 - Qiita")

Test-Kitchenの1.4でVerifierがモジュール形式になり、Busserが唯一の共通処理からいちモジュールに身を落とした時点でこうなるよなーと。

マージには足掛け半年くらいかかったわけだが、前回`exec`コマンドを追加するプルリクエストを送った時もそのくらいかかった。

今回はChef Incの人が来日した際にその場にいた[@dai_lxr](https://twitter.com/dai_lxr)にPushしてもらったのがよかったのかもしれない。

<blockquote class="twitter-tweet" data-conversation="none" lang="ja"><p lang="ja" dir="ltr">Q. kitchen-verifier-shellのPRを取り込んで！ <a href="https://t.co/IjmguH3YNq">https://t.co/IjmguH3YNq</a>&#10;A. 取り込まれてよい状態だと思う。私からも言っておくがメンテナに再度pingしてほしい。 <a href="https://twitter.com/hashtag/chef_docker?src=hash">#chef_docker</a> <a href="https://twitter.com/hashtag/getchef?src=hash">#getchef</a> <a href="https://twitter.com/sawanoboly">@sawanoboly</a></p>&mdash; dai / Chef活用ガイド発売中 (@dai_lxr) <a href="https://twitter.com/dai_lxr/status/664446367159484416">2015, 11月 11</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

英語書くのが面倒だからIssueでの議論をスキップして突然プルリクってのは、タイミングが合わない時にお互い困るのかな。
