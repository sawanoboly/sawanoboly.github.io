+++
date = "2014-03-05T00:00:00+09:00"
draft = false
slug = "vagrant-with-chef-zero"
tags = ["chef", "vagrant"]
title = "Chef-Server環境で使うCookbookをVagrantとChef-zeroで開発する"

+++

最近はChef-Severの導入も簡単になりました。 [パッケージから導入](http://www.getchef.com/chef/install/)はもちろん、[aws marketplace](https://aws.amazon.com/marketplace/pp/B010OMNV2W)からすぐ使用できたりと。

とはいえテスト環境にChef-Serverを強いるのも大変なので、Chef-Zeroで代わりをしてもよいでしょう。

## Chef-zero

- [https://github.com/chef/chef-zero](https://github.com/chef/chef-zero)

Chef-zeroはChef-Serverのホットモックのようなものです。ほぼChef-Serverとして動作する小さなプロセスですが次の特徴があります。

- 認証はするフリで全部OK
- データはメモリに保存、永続化なし

Chef-Serverを使ったCookbookの開発をする際に、データをクリアしたい場合もありますが意外と後処理が面倒です。 Chef-zeroならプロセスのリスタートで綺麗な状態になるので、knifeで必要な要素を戻すだけで再度テストを行なうことができます。

詳細は次のリンクが参考になります。

- [軽量簡易Chef Server「chef-zero」を使ってみよう #opschef_ja « CREATIONLINE, INC.](http://www.creationline.com/lab/2749)

## VagrantでChef-zero

ではChef-zeroのプロセスをどこに立てるか。[test-kitchen](https://github.com/test-kitchen/test-kitchen)ではCookbookをテストするVMで直接Chef-zeroを起動して、ダミーのChef要素を入れることができますが、現時点ではそのままシームレスにChef-Server環境に持って行こうとするにはいまいち感があります。

じゃあVagrantだろうなということで、一式書いてリポジトリに登録しました。

- [https://github.com/OpsRockin/vagrantwithchefzero_example](https://github.com/OpsRockin/vagrantwithchefzero_example)

![](./images/2014/vagrant_with_chef-zero.png)

これならvagrant up chefzeroするだけでChef-zeroをほぼChef-Serverとして使い放題です。

chefzeroのVMだけはChef-Soloで構築しますが、ほかのVMはChef-zeroに対するChef-ClientとしてProvisioningすることができます。

最初は手元だけ直して、Cookbookのアップロードを忘れることもありますが、すぐ慣れるでしょう。

通常操作はknifeを使えるので、他の環境に持っていく際にknife.rb指定の切り替えで済むのが助かりますね。


## 開発作業用のRakefile

レシピ開発＆テストを効率よく行なうには、とりあえずRakeのタスクを作っておくとよいです。

```ruby
task :default => 'upload'
$stdin.sync = true

desc 'upload chef-repos to chefzero vm'
task :upload do
  system('knife cookbook upload --all')
  system('knife upload environments/ ')
  system('knife upload roles/ ')
end

desc 'reset chefzero'
task :reset do
  system('vagrant reload chefzero')
  Rake::Task['chefzero:upload'].invoke
end
```

それぞれこんな作業をやらせています。

```bash
rake upload # Chef要素のアップロード
rake reset  # Chef-zero内容クリア＆要素の再アップロード
```

## その他

ちなみに[vagrant-chefzero](https://github.com/whitepages/vagrant-chefzero)というプラグインもあり、これはEnvやRole要素の投入までVagrantファイル内でやるようです。

knifeを使わないのが逆に気になるので今回はCookbookでChef-zeroをセットアップしてます。
