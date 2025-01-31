---
layout: post
title: Relationship between local coordinates and thermal conductivity in amorphous carbon
summary: |-
    　新作でました
author: Emi Minamitani
---
今年度の最初の新作でました。テーマもおニューのものなので嬉しいです。["Relationship between local coordinates and thermal conductivity in amorphous carbon"](https://avs.scitation.org/doi/10.1116/6.0001744)

分子研が購読してない雑誌と思われるので、Open Accessにしました。
博士をとってから随分になるので、若手とはあまり言えない年なのですが、
アメリカ真空学会の若手応援キャンペーン的な特集号とのことで、
著者略歴入りみたいです。ちょっと気恥ずかしいですね。内容は、使ってみたいとあれこれ試していたパーシステントホモロジーの物性方面への応用です。

パーシステントホモロジーは、「パーシステンス」の概念が入ることで、不変量より細かい情報を拾えて、トポロジー的な特徴と、幾何学的な情報の両方がいるような構造の特徴をうまく捉えてきているテクニックだなぁと思っています。完全にランダムではなくて、何かしらの拘束条件が背後にあるんだけれども複雑なものの構造解析には有用だというのは[先行研究](https://www.pnas.org/doi/abs/10.1073/pnas.1520877113)を読んでてひしひしと伝わってきたので、自分も使いたいなぁと思いました。個人的に興味があるのはそれを物理量の予測に使うことで、いくつかの方向を試しています。数学的なバックグラウンドもあれこれ勉強していますが完全には理解していないものの、日本でオープンソースを開発されている研究者の方がとても丁寧な解説付きの[公式サイト](https://homcloud.dev/)を運営されているため、思ったより早く実戦投入できました。

今回のものは、縁あってアモルファス関係を研究している人たちと知り合った＆熱伝導関係での研究をすすめていた＆パーシステントホモロジー面白そう、という偶然で、アモルファスの熱伝導率と構造をパーシステントホモロジーで結びつけようという研究を思いついて始まった共同研究の結果です。

あと、Allen-Feldman理論というテクニックをつかったらDFT計算ベースで何とかアモルファスの熱伝導率だせるんじゃね、という脳筋な思いつきを実行した論文でもあります。そのために[コード開発](https://github.com/eminamitani/thermal_conductivity_code)もしたので、興味がある人が居たらどうぞ。

アモルファスカーボンの中の環状構造の特徴と熱伝導率の関係がそこそこ解析できたので、当初の予想よりうまく行ったなというのが個人的な感想です。あとレフェリーコメントの"I really like this piece of work. I have long thought about doing this myself, and I am glad to see that these authors have. "にすごく勇気づけられました。自分も「ええやん」って論文を査読したときはこれぐらいencouragingなコメント書こうと思いました。

今回の論文は実は第２弾として書いていたもので、第一弾はまだ紆余曲折あって査読段階です[(Arxiv)](https://arxiv.org/abs/2107.05865)。こちらのターゲットはアモルファスSiで、使っているのは古典MDですが、環状構造と熱伝導率の関係の逆解析はこちらのほうが丁寧にあれこれ試しているので、これも早く何とかなるといいなぁ。