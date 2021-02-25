---
layout: post
title: Webサイト作り出しました
summary: |-
    　Webサイト構築手順のまとめ
author: Emi Minamitani
---
研究室が立ち上がって早2年弱、Webサイト作成を放置していたのですが、
ようやく作ってみることにしました。
CSSとかをいじったり、SCPとかでファイル転送や固有のエディタを使うのが面倒だったので、jekyll+Github pagesでMarkdownでコンテンツが書けて管理が楽なサイトを作ろうと思いました。結構詰まったところも多かったので、構築手順を備忘録として書いておこうと思います。

## jekyllなどの環境構築
`jekyll`はRubyで作られた、静的なウェブサイトを作るためのツールで、GitHub上のソースからWebページを作るGitHub Pagesでのビルドにも使われています。Markdownで書いた文章をサクッとウェブサイトにしてくれるのが魅力です。GitHub Pagesでデプロイする前に、テストするために、自分の手元にもRubyと`jekyll`が入った環境を作ります。ここではMacOSで行った手順を紹介します。

### Ruby, rbenv,jekyllのインストール

RubyとRubyの仮想環境を管理するための`rbenv`を`homebrew`でインストールします。

```
brew install ruby
brew install rbenv
```

`rbenv`での環境の切り替えを反映できるように、PATHを通します。
bashの場合
```
echo '# rbenv' >> ~/.bash_profile
echo 'export PATH=~/.rbenv/bin:$PATH' >> ~/.bash_profile
echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
source ~/.bash_profile
```
です。zshの場合は`.zshrc`を変更します

最新のRubyだとバージョンの不整合が起きたので、一つ前のバージョンのRubyの環境を作りました
```
rbenv install 2.7.2
rbenv global 2.7.2
```
これでバージョン2.7.2の仮想環境ができているはずです。

```
$ which ruby
/Users/xxx/.rbenv/shims/ruby
```
のように仮想環境のパスが反映されているか確認しておくと無難です。

この仮想環境下に`bundker`と`jekyll`を以下のコマンドで導入しました。
```
gem install --user-install bundler jekyll
```

### jekyllのテーマの活用
`jekyll new` でフロムスクラッチで作ることもできるのですが、見た目のよいWebSiteを作ろうとした場合、色んな人が公開してくれているテーマを利用するのが手早いです。
たとえば[jamstackthemes.dev](https://jamstackthemes.dev/ssg/jekyll/)などでいろんな例が公開されています。むっちゃありがたいですね。今回は[Bulma Clean Theme](https://jamstackthemes.dev/theme/bulma-clean-theme/)を使うことにしました。
このテーマの[githubレポジトリ](https://github.com/chrisrhymes/bulma-clean-theme)で自分のアカウントにForkして、そのForkしたリポジトリを`git clone` してきます。

`git clone` してできたフォルダの中で、`Gemfile`を必要に応じて変更します。
今回は[開発元の推奨設定](https://www.csrhymes.com/2020/05/08/creating-a-docs-site-with-bulma-clean-theme.html)を使って以下のように
Gemfileを書き換えました
```
# frozen_string_literal: true

source 'https://rubygems.org'
gem "bulma-clean-theme",  '0.7.2'
gem 'github-pages', group: :jekyll_plugins
```
それと、github pagesでテーマを反映させるために
`_config.yml`に
```
remote_theme: chrisrhymes/bulma-clean-theme
```
を追加します。その状態で
```
bundle install
```
で、必要なパッケージをインストールします。
ここまで進んだ状態で
```
bundle exec jekyll serve
```

とやって`http://127.0.0.1:4000/`にアクセスしてうまくWebサイトの体裁ができているかを確認します。

うまく行っていれば、`_config.yml`, `_data/navigation.yml`とかを適宜いじって、いらないファイルは消して、git commit, pushします。

あとはgithub pagesでの[公開手順](https://docs.github.com/ja/github/working-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)の手続きを行って、うまくデプロイされるのを待ちます。

Githubのリポジトリの右下の`Environments`のところに`github-pages`というのができていると思うので、それをクリックしてDeproymentsのページに行って、`View deployment`をクリックすると作られたWebサイトが確認できます。

## 意外に詰まったところ
画像のurlの指定につまりました。とりあえずプロジェクトルートに`images`というフォルダを作って、そこに表示したい画像を入れる→github上にアップロードしてから、githubでその画像自体の絶対パスを取得して、それを使わないとうまく表示できませんでした。
`https://github.com/eminamitani/website-under-construction/blob/master/images/e-ph.png?raw=true` みたいな感じの最後に`raw=true`が入っているurlを使うとうまく行ったのですが、もっと賢いやり方もありそうです。