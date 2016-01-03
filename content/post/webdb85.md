+++
date = "2015-02-23T00:00:00+09:00"
draft = false
slug = "webdb85"
tags = ["magazine","aws","opsworks"]
title = "WEB+DB PRESS vol.85のAWS自動化特集でOpsWorksの記事を担当しました"

+++

雑誌の概要はこちら => WEB+DB PRESS Vol.85｜技術評論社 。

{{% img-responsive "/images/2015/osh2015-chef.jpeg" "写真はオープンセミナー広島2015での発表から" %}}

共著のみなさんも既にブログで紹介されています、各章の対象読者や雑誌のオススメ具合はこちらがとても参考になります。

- [@sgwr_dts(winebarrel)](https://twitter.com/sgwr_dts) さんの記事 => [AWS as Code!: WEB+DB PRESS Vol.85に記事を書きました - so what](http://so-wh.at/entry/2015/02/21/AWS_as_Code%21%3A_WEB%2BDB_PRESS_Vol_85に記事を書きました)
- [@muramasa64](https://twitter.com/muramasa64) さんの記事 => [WEB+DB PRESS vol.85にCloudFormationの記事を書いた - 雑記帳(2015-02-23)](http://muramasa64.fprog.org/diary/?date=20150223)
- [@y15i(y13i)](https://twitter.com/y015i) さんの記事 => [t.y13i.com — WEB+DB PRESS Vol.85にCloudFormationの記事を書きました](http://t.y13i.com/post/111752004680/web-db-press-vol-85-cloudformation)

特集を簡単に紹介すると、

- 導入しやすく、まあまあ普及感のあるCloudFormationの現場ノウハウ
- 自分の担当したOpsWorksは(多分世間に求められている程度の)Getting Started
- 既存の環境もコードにするための思想と、それのリファレンス実装ともいえる[Codenize.tools](http://codenize.tools/)の紹介

という内容です。

詳細はもうタップリ出てますし、実物も本屋にも並んでいます。折角なので少し記事の裏側で個人的に面白かった話などを書きます。

## 記事の経緯を聞いたよ

できあがりは『AWS特集』でしたが、原案は[@gosukenator(mizzy)](https://twitter.com/gosukenator)さんとのこと。

> [Infrastructure as Code 現状確認会(2014年10月)](http://togetter.com/li/727057) のあとに、クックパッドの人たちが開発してるIaC(※Infrastructure as Code)なツールで記事書けるんじゃないか、と稲尾さんに企画を持ち込んで、打ち合わせをした結果、AWSにフォーカスした話にしましょう、ということになって、ああいった企画になりました。

談: [@gosukenator(mizzy)](https://twitter.com/gosukenator) さん

そのままいけばクックパッドの[Codenize.tools](http://codenize.tools/)や[Itamae](http://itamae.kitchen/)、[Infrataster](http://infrataster.net/)ほかの記事になっていたのかもしれませんね。  
(これはこれで読みたい方も多そうだし、雑誌から盛り上げるような方向もあっていいような気はしました。)

## Codenize.tools

{{% img-responsive "/images/2015/Codenize_tools.png" "Codenize.tools" %}}

クックパッドインフラツール達の何が特集企画として持ち込まれるほど面白いのかっていうと。

昨今`Infrastructure as Code`(以下、IaC)と言いつつも、色々と手を出しているうちにどうもツールに使われているような感覚に陥ることがあります。  
便利そうなツールが出揃っているように見えても、自分の抱える問題を本質的に改善するためには、その手のお作法の学習コストや変態的ハック、またはツールの更新にいちいち振り回されるのは割りに合いません。

現在OSSとして公開されている各種ツールも、そもそもは開発者が彼ら自身のフローを自動化するためにはじまったのでしょう。

で、IaCのきっかけとも言えるインフラリソースのAPIに対して自身のフローをもって真正面から向き合っているCodenize.toolsのは根底思想からしてやっぱり面白いと思うわけです。

ちなみに[Codenize.tools](http://codenize.tools/)という名称は、AWS自動化記事の校正段階である全体タイトル決め(Issue#60)の最中につけられました。  
他の章はサービス名なので分かりやすいのですが、『クックパッド独自ツール』では微妙だなーという話になったので、じゃあグループ名をつけたらどうでしょう？と。

> 例えばCookpad suites(スイーツ)とか、ツールに一連のシリーズ名があるとぐっといい感じですよね。
>
> Github Issue #60 @sawanoboly 発言より

私の提案(名前をつけるという部分で)は24時間以内に[Codenize.tools](http://codenize.tools/)の公開を持って採用されました。

<blockquote class="twitter-tweet" lang="ja"><p lang="ja" dir="ltr">なまえつけたよ！ <a href="http://t.co/BHEJBBMpe9">http://t.co/BHEJBBMpe9</a></p>&mdash; auto.conf (@sgwr_dts) <a href="https://twitter.com/sgwr_dts/status/552846637249208322">2015, 1月 7</a></blockquote> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>

実際私が本特集で一番気に入っているのは4章の『Codenize.toolsによるメンテナンスの自動化』、その中でも「Codenize.toolsのしくみ」で密かに語られるコードを用いて運用するということへのこだわりの部分です。

## Githubでの記事執筆

技評さんとのお仕事、ひいては雑誌に記事を書くことは今回がはじめてでした。  
噂のとおり編集、執筆者同士のやりとりはほぼすべてがGithubで行われ、やたらと快適でした。これは確立した先人達に感謝。

{{% img-responsive "/images/2015/webdb85-sp01-01.png" "Githubの記事リポジトリ" %}}


私は結構サボるタチなのでリモート向いてないんですが、編集の[@inao](https://twitter.com/inao)さんのGithubまわりのテンションが高く、見ていると『あー、やらなきゃなー』という気分になりました。 開始時のWeb会議以降はすべてGithubでのやりとりだったので、リモートでの雰囲気作りは大事だよねと再認識。

今回私がリポジトリのオーナーだったので、実は自分だけSlack連携で流れを追うのに少しばかり楽をしていました。共著の皆さんごめん。

{{% img-responsive "/images/2015/webdb85-sp01-02.png" "" %}}

## 菅原式レビューボード

Githubでの記事執筆に関連して、今回面白かった事件を1つ。

個人の作業としては快適なGithubですが、相互レビューの段階で次の問題がありました。

- コミット単位でレビューを付けると全体的に追いづらい
- Issueにレビューを書くとしても、どの時点のどの部分を指しているか分かりにくい事がある

> このへんは結構共通の認識のようで、[Reviewable](https://reviewable.io/) とかレビューのための外部サービスがあります。

この問題、[@sgwr_dts(winebarrel)](https://twitter.com/sgwr_dts)さんにより次の手法が提案されました。

1. ブランチを作成
1. 章ごとの原稿(まとまった1ファイル)を消してコミット
1. 2をrevertコミット
1. Pull Request作成!
1. 各自コメントをrevertコミットに対して付け、それの返信で議論＆対応する。

{{% img-responsive "/images/2015/webdb85-sp01-03.png" "菅原式レビューボード" %}}

プログラムではやってはいけないですが、blameが少々潰れても構わない原稿のレビューにおいてはシンプルで導入しやすく、レビュー対応もほとんど漏れない(幾多のレビューコメントで私が2個ほど漏らしたのみ)という中々素晴らしい手法だと思いました。  
レビュー及び対応は、各段階の区切りで次のように見やすくなっています

{{% img-responsive "/images/2015/webdb85-sp01-04.png" "1つのコミットにある時点の原稿に対するすべてのレビューが集約" %}}

手法は名称がつくと流行りやすいこと、個人的に苗字＋技名は浪漫と思っていることから、この手法を私は『菅原式レビューボード』と名づけました。浸透はしませんでした。

## おわりに

なんやかんやで、共著の御三方[[@sgwr_dts(winebarrel)](https://twitter.com/sgwr_dts), [@muramasa64](https://twitter.com/muramasa64), [@y15i(y13i)](https://twitter.com/y015i)]と編集の[@inao](https://twitter.com/inao)さんのおかげで良い感じにまとまりました。  
機会があればまた書いてみたいですね。

さて、とりあえず自分の関わった特集の事を書きました。  
他にも(いただいた見本誌を読んでいると)『リアクティブプログラミング』、『楽しもうOSS開発』、『Railsらくらくテストデータ準備』、『Consulでクラスタ管理』と読み(試し)応えのある記事が沢山あるので、ぜひご一読を。
