+++
date = "2014-03-17T00:00:00+09:00"
draft = false
slug = "jaws-days-2014immutable-infrastructure"
tags = ["event"," immutable_infrastructure", "infrastructure_as_code"]
title = "JAWS DAYS 2014のImmutable Infrastructure(II)トラックで話した"

+++

[JAWS DAYS 2014](http://jawsdays2014.jaws-ug.jp/)セッションで話しました。


## Infrastructure as Codeと 組織のドキュメンテーション ＋ Immutable Infrastructure事例

スライドはこちら。

<iframe src="//www.slideshare.net/slideshow/embed_code/key/3mW6FcWxSUvS6Q" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/YukihikoSawanobori/jawsdays-infra" title="Infrastructure as Codeと 組織のドキュメンテーション ＋ Immutable Infrastructure事例" target="_blank">Infrastructure as Codeと 組織のドキュメンテーション ＋ Immutable Infrastructure事例</a> </strong> from <strong><a href="//www.slideshare.net/YukihikoSawanobori" target="_blank">Yukihiko SAWANOBORI</a></strong> </div>


現場にいらっしゃった方の反応などもTogetterにまとめて頂いています。 [2014/03/15 JAWS DAYS 2014 - Immutable Infrastructureトラック #jawsdays #infra - Togetterまとめ](http://togetter.com/li/639910)

### 『Immutable Infrastructure』事例について

大阪某所でやっているGiraffiから事例を紹介しました。

[FBページ:Giraffi DevOps](https://www.facebook.com/giraffi.devops)

当時はImmutable Infrastructureなんて'名前'は全く意識していません。 構成を検討する際ベースにしたのは、従来のサービス死活監視に対する不満でした。

サービス継続性とサーバ単体の死活が直結しないよう抽象化し、アラートの粒度や優先度を分類して、即時対応しなければいけないケースを減らそうという目的で構成をDeveloperと一緒に検討したんですよね。

その結果、従来のSPOFはDisposable Componentsになり、運用の方法としてImmutableも選択できる構成となった事例です。

パネルセッションでは言いそびれましたが、どの層が『Immutable Infrastructure』するべきか、また恩恵をうけるかというところでは、『夜中にアラートで起こされる人』こそが。じゃんじゃんやるべきなのかもしれませんね。

### 『Infrastructure as Codeと 組織のドキュメンテーション』について

『Immutable Infrastructure』トラックでなんか話せと言われてから、普通に説明したって全員被るに決まってるがなと思いました。 それならば、説明は他のセッションにまかせて、前提知識である『Infrastructure as Code』の導入支援をつくろうとしたらこうなりました。

この話はもともと、『手順書は芸術品ではないか？』という発想から始まっています。

で、その会話とクヌース先生の文芸的プログラミングあたりの概念を結びつけた@azukiwasher氏によって書き起こされたのが『ドキュメント礼賛(原題：手順書礼賛)』です。

[gist:8571505](https://gist.github.com/azukiwasher/8571505#file-gistfile1-txt)


> ## ドキュメント礼賛
>
 
> 断言しよう。ドキュメントこそがマシンを隷属化し、人間のために使役させるための最善の方策である。  
> なぜなら、マシンをコントロールする「ドキュメント」を理解できるのは人間だけであり、
> マシンは決してそれを理解することはできないからだ。
>
> ドキュメントは、マシンにとって高級すぎる別世界の読解不可能な暗号だ。読解不可能という点が人間
> にとって甚だ都合がいい。人間の企みがマシンに理解されてはおしまいなのだから。  
> 企みは、マシンから秘匿され秘密裏のうちに人間の「手」によって着実に行使されねばならない。
>
> 一方、ドキュメントは人間によって書かれ、それを「玩味」できるのも人間だけである。
> 人間だけがドキュメントの行間を読み、レトリックを味わい、隠された意味を発見できる。  
> 「作者」の意図を自らに与えられた使命に、いま誇らしげにキーボードを叩きはじめる・・・。
> そしてすべての手順が終わったとき、彼は作者との一体感を感じ、ドキュメントをシェアしあう、
> 親密なコミュニティ、やさしい仲間たちに迎え入れられる。
>
> ここに強い人間たちの結束が生まれ、ついにはマシンをコントロールし人間のために永遠に
> 労働しつづける奴隷たらしめることに成功する。
>
> 繰り返す。ドキュメントこそがマシンを隷属化し、人間のために使役させるための最善の方策である。
>
> 決してその逆ではない。

『手順書は芸術品ではないか？』 => 『洗練される』 => 『実行可能な機能美』 => 『Chefのレシピなどに行き着く』 => 『真の手順書とは(いわゆる)コードなのだ』

この文書が面白かったので、人類向けのセッションに起こしてみました。

最近ちょいちょい『ChefおよびInfrastructure as Code』について話を聞きたいという声をかけて頂いて幾つか訪問したり、実際にトレーニングをさせて頂いてますが、やはりみなさん難しく考え過ぎだったり、新しいことへの抵抗勢力があったりと結構モヤモヤするんですよ。

イベント会場にいらっしゃる方々は、『Infrastructure as Code』の何がよいかなんて十分ご存知でしょうが、さて自社に持ち込んで〜となるとやはり困る。それをどのように説得するかの一例として考えたメソッドとしても使えるとよいなと考えて、かなり回りくどく色々と言ってみましたがどうだったでしょうか。

発表後の質問でもでましたが、あらためてアプローチの例を並べてみました。

- 権威を呼んでくる
- 対象層向けの本を出す
- 自分が出世する
- 実際にやって見せる

個人的なおすすめは、『実際にやって見せる』ですね。

例えば社内でのクラブ活動をしてみる。結局、業務内で何とかしようとするのは中々難しいです。 だったら自腹でクラウド使ったり、内輪のシステムを作って研究発表なんかするのが遠回りのようで一番効果的のように思います。

## パネルセッション

お会いしたかった方々と、まとめて会えるという個人的に嬉しい企画でした。 このトラックに放り込んでくれた[@urasoko](https://twitter.com/urasoko)さん、そのあと取りまとめいただいた[@stanaka](https://twitter.com/stanaka)さんほかスタッフの方に感謝！

パネルセッションのまとめはこちらに => [JAWS DAYS 2014 Immutable Infrastructure パネルディスカッション - stanaka's blog](http://blog.stanaka.org/entry/2014/03/15/215437)


時間の都合で普通に喋れなかったのがはちと残念ですが、この日は[@mizzy](http://mizzy.org/)さんとたっぷり密談したので、今後はその成果を出していきたいですね。
