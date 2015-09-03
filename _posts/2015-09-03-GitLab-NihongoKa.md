---
layout: post
---

GitLab.media.eng.hokudai.ac.jp の日本語化
====

150810

##参考URL
http://qiita.com/ksoichiro/items/f63196aa0a7f07c1862d
https://github.com/ksoichiro/gitlab-i18n-patch

##現状
原稿のバージョンは GitLab 7.10.1 で，対応する日本語パッチはない．
7.10.0 用のパッチを上記サイトの手順通りに適用してみると表示が崩れた．

```sh
# cd /opt/gitlab/embedded/service/gitlab-rails
# patch -R -p1 < ~/app_ja.patch
```

としてリバースパッチし，もう一度プレコンパイル，すなわち

```sh
# cd /opt/gitlab/embedded/service/gitlab-rails
# rm -rf public/assets
# export PATH=/opt/gitlab/embedded/bin:$PATH
# bundle exec rake assets:precompile RAILS_ENV=production
```

を実行すると直った．


上記の操作は，今そこに走っている実環境に対して直接手を加えてしまったものであり，後悔．
リバースパッチに失敗していたらどうなっていたか．
そのための安全線が「Vagrantで試す」ことであるようだ．
Vagrantは，VirtualBox などの仮想化ソフトを操って．即席のテスト用仮想化環境を構築してくれる．
パッチを本当に当ててしまう前に，まずはテスト環境を立ち上げて結果を確かめてみたほうがいいという考え方．実は当たり前なことかもなあ．

Vagrant の操作方法は，パッチのレポジトリの README.md
https://github.com/ksoichiro/gitlab-i18n-patch
にも書いてあったが，ちょっとよくわからなかった．

結局，日本語化するには，GitLabに対し日本語化パッチが対応しているバージョンへのアップグレードが必要なようだ．

GitLabのアップグレードの手順を調べてみると，
http://doc.gitlab.com/omnibus/update/README.html#updating-from-gitlab-66-and-hgitlab-ctligher-to-the-latest-version
gitlab-ctl コマンドを用いて行うことができるようだ．

ただ，手順としては，

* Stop all GitLab services;
* Create a backup using your current, old GitLab version. This is a 'light' backup that only backs up the SQL database;
* Run gitlab-ctl reconfigure, which will perform any necessary database migrations (using the new GitLab version);
* Restart the services that were running when the upgrade script was invoked.

と，ちょっとデータベース周りの手間の掛かる作業が必要な雰囲気なので，今日はここでやめにした．

