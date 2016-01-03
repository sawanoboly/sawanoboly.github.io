+++
date = "2015-12-07T00:00:00+09:00"
draft = false
slug = "ad2015-amimoto_die_hard"
tags = ["test-kitchen","ci","integration"]
title = "AMIMOTO DIE HARD - 死んでも死なないタフさが必要だ。"

+++

この記事は[AMIMOTO Advent Calendar 2015](http://www.adventar.org/calendars/1107) 、12月7日(月)のエントリです。


{{% img-responsive "/images/2015/amimoto_banner.jpg" "" %}}


## あらすじ

[AMIMOTO AMI](https://ja.amimoto-ami.com)はAWSのPublic AMIとして生まれ、好評を博したのちAWS MarketPlaceに進出した[WordPress](https://wordpress.com/)のAMIである。自前で構築するより大抵楽で、今日ではそこいらのWordPress環境より当然応答が速いチートガイに成長した。

(調べればすぐ判ることなので)詳細は割愛するが、AMIMOTOでは最新のWordPressおよびミドルウェアにいち早く対応するため、AMIには外部処理の取得手続きのみを埋め込み、起動時にWordPressをセットアップしている。


物語はAMIMOTOを起動したユーザの、ある連絡で幕を開ける。

『WordPressが動いていない。』

セットアップを前述のポリシーで行っているAMIMOTOでは、まれに起こるとされる既知の問題としてデジタルキューブ組長も頭を抱える。  
原因はたいていAmazon LinuxのRPMリポジトリ更新による`yum update`か、セットアップ手順の変更に起因していた。

少なくともユーザより先に(AMIからみた)外部要因による問題の発生を検知し、いち早く修正を行なえるようにしたいという相談に対して私が提供した仕組みとは．．．？


## 主要登場物

<dl>
<dt><a href="https://ja.amimoto-ami.com">AMIMOTO AMI</a></dt>
<dd>特徴はあらすじを参照。<br />シリーズにはVirtualizationのタイプに応じたPVM版とHVM版、およびPHPの実行エンジンにHHVMを採用した(タイプはHVM)の3種類がある。</dd>
<dt><a href="https://github.com/test-kitchen/test-kitchen">Test-Kitchen</a></dt>
<dd>マシンリソースの調達/破棄を管理し、プロビジョニングとテストを実行するツール。<br />今作ではCircleCI上で稼働し、AMIからインスタンスの作成およびyum updateを実行、Infratasterの起動を担当した。</dd>
<dt><a href="https://github.com/ryotarai/infrataster">Infrataster</a></dt>
<dd>インフラの振る舞いをテストするフレームワーク。<br />得意のCapybaraを使い、WordPressの振る舞いテストを行なう。Javascriptのエラー程度ならば大目に見る(※設定次第)寛大さを持つ。</dd>
<dt><a href="https://circleci.com/">CircleCI</a></dt>
<dd>CI、いわゆるContinuous Integrationのサービス。<br />Test-Kitchenの実行と、安否のレポートをする。家訓を守り10分間の沈黙は死とみなすが、今作では回避手段をとられてしまう。</dd>
<dt><a href="https://www.heroku.com">heroku</a></dt>
<dd>アプリケーション実行のプラットフォーム。<br />裏設定としてスケジュールタスクが登録されており、CircleCIのビルドを定期的にキックする役で登場。</dd>
<dt><a href="https://ifttt.com/">IFTTT</a></dt>
<dd>条件によってシンプルなタスクを実行するサービス。<br />セットアップタスクの変更をRSSで取得し、CircleCIのビルドをキックする遊撃手として暗躍する。</dd>
</dl>



## 『AMIMOTO DIE HARD』 解説

結論からいうと、次の条件でテストを回すことにした。対象はAMIMOTOシリーズ全部。

- 定期的、毎日一回。
    - リポジトリの変更によるクラッシュを検知。
- セットアップ用コードの変更。
    - セットアップの不備を検知。

後者もそもそもが、何かのパッケージ更新との合わせ技による発生が主ではあったようだ。  
AMI起動からの大元の仕組みが変わることも考慮にいれて、『ただWordPressがつかえる状態か』のみを確かめる仕組みとしている。

実施している内容は主要登場物のセクションに書いている通りだが、いくつかピックアップして解説しよう。


### Infratasterによるテストの内訳とコード

テストの段取りは次の通り。

1. AMIMOTOのお約束、インスタンスIDの入力において(所有者確認)
    1. 間違ったIDを入力してハネられること。
    1. 正しいIDを入力し、WordPressのインストール画面に移ること。

とりあえずここまでやれば、関連サービスがすべて正常に稼働しているとわかる。
Infrataster(Capybara)では次のようにコードを書いた。

```ruby
describe server(:amimoto) do
  describe capybara('http://amimoto') do
    it "Blocks invalid string" do
      visit '/wp-admin/install.php'
      fill_in "instance_id", with: 'hogehoge'
      click_button 'Next Step'
      expect(page).to have_content "Sorry, that isn’t a valid instance ID."
    end

    it "Unlock Success" do
      visit '/wp-admin/install.php'
      fill_in "instance_id", with: ENV['KITCHEN_SERVER_ID']
      expect(click_button 'Next Step').not_to be_nil
    end

    it "Should got 'Welcome' after unlock" do
      visit '/wp-admin/install.php'
      expect(page).to have_content 'ようこそ'
    end
  end
end
```

インスタンスID(環境変数)は、Test-Kitchenの機能。

### Test-Kithcen + Shell-Verifier

AMIMOTO DIE HARDの本体はこのTest-Kitchenといえる。

1. AMIからインスタンスを作成する。
1. Shell-Provisionerで`yum update`をかけ、関連サービスを再起動。
1. Shell-VerifierでInfratasterを実行。
1. テストの結果にかかわらず、インスタンスを破棄。

インスタンス関連の設定はただec2ドライバを使ってるだけ。`.kitchen.yml`をそのまま貼ると次の通り。

末尾に突然入っているRubyコードはCircleCI向けの小狡い手段だ。

```yaml
---
driver:
  name: ec2
  region: ap-northeast-1
  availability_zone: ap-northeast-1c
  subnet_id: <%= ENV['AWS_SUBNET_ID']  %>
  associate_public_ip: true
  instance_type: t2.micro

transport:
  username: ec2-user
  aws_ssh_key_id: <%= ENV['AWS_SSH_KEY_ID']  %>
  ssh_key: <%= ENV['AWS_SSH_KEY_PATH'] %>

provisioner:
  name: shell

platforms:
  - name: hiphop-amimoto
    driver:
      image_id: ami-xxxxxxxx
  - name: pvm-amimoto
    driver:
      image_id: ami-xxxxxxxx
      instance_type: m1.small
  - name: hvm-amimoto
    driver:
      image_id: ami-xxxxxxxx

verifier:
  name: shell
  command: bundle exec rspec -fd

suites:
  - name: default
    run_list:
    attributes:
<%
## Periodic outputs for CircleCI
if ENV['CI']
  ppid = Process.pid
  Process.fork {
    loop {
      puts "."
      begin
        Process.getpgid( ppid )
        sleep 20
      rescue
        exit 0
      end
    }
  }
end
%>
```

導入当初、PVMのインスタンスタイプにはt1.microを使っていた。  
しかしこのインスタンス、WordPressのセットアップ完了直後に`yum update`をかけると、わりと高い確率で暫くの間まともに応答しなくなっていた。そのまま10分ほどCircleCIでアウトプット無しの状態が続くと、Failで終わってしまう。

しかたがないので何か出力しておくため、Test-Kitchen実行時にダミー出力を行うプロセスをフォークで作ったのが末尾のRubyコード部分だ。  
通常、`circle.yml`などでバックグラウンドプロセスを起動すると、安易な時間切れ対策対策？として出力はミュートされる仕組みになっているが、テスト内でのフォークは流石に捕まらないようだ。  

結局t1.microは時間がかかりすぎる事が多かったので、先日smallに変更した。強制終了まで2時間(!)かかることがあったりもしたし。


### herokuからCircleCIのビルドをキック

CircleCIは定期実行する仕組みを提供していない(目的外だし)ため、任意のブランチのHEADをビルドするRakeタスクを作成。

```ruby
require 'circleci'
require 'logger'
@logger = Logger.new($stdout)

CircleCi.configure do |config|
  config.token = ENV['CIRCLECI_TOKEN']
end

task :default do
  begin
    @logger.info "Run nightly integration"
    res = CircleCi::Project.build_branch 'Launch-with-1-Click', 'amimoto_die_hard', 'master'
    @logger.info res.code
    unless res.success
      @logger.error "Maybe fail, it will notifies to some service."
    end
  rescue => e
    @logger.error e.class
    @logger.error e.message
  end
end
```

あとはこれをherokuにおいて、スケジューラから定期的に実行する。

## まとめ

もともとコケるたびにデジタルキューブさん側で対策込みの改善もしていたので、もうこれにほとんど引っかかったりはしません。  
ただ、意識外で起こる不思議な障害のなるはや検知には、抜き打ちテストを回しておくのがいいよね。


## 関連項目

- [AMIMOTO Advent Calendar 2015](http://www.adventar.org/calendars/1107) ひとつ前の記事 => [[AMIMOTOアドベントカレンダー]WordCamp US現地からブースの様子をお送りします - WP-kyoto](http://wp-kyoto.net/go-to-wordcamp-us/ "[AMIMOTOアドベントカレンダー]WordCamp US現地からブースの様子をお送りします - WP-kyoto")
- [AMIMOTO Advent Calendar 2015](http://www.adventar.org/calendars/1107) 次の記事 => [AMIMOTOブース in WordCamp USフォトレポート | Firegoby](https://firegoby.jp/archives/6450 "AMIMOTOブース in WordCamp USフォトレポート | Firegoby")


## 外部リンク

- [超高速 WordPress AMI AMIMOTO](https://ja.amimoto-ami.com/ "WordPress 専用の AWS + クラウドホスティング AMIMOTO | 超高速 WordPress AMI AMIMOTO")

