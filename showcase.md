---
title: Links
subtitle: 共同研究先や研究上で便利なサイトのリンク集
description: summry of the link for collaborators and very helpful website
layout: page
#showcase: showcase_example
show_sidebar: false
---

## 共同研究先
- [京都大学 総合人間学部　高木研究室](http://www.nanosci.h.kyoto-u.ac.jp/)
- [東京大学大学院工学系研究科マテリアル工学専攻 渡邉研究室](http://cello.t.u-tokyo.ac.jp/)

## リンク集

### 共同利用計算機センター
- [自然科学研究機構　岡崎共通研究施設　計算科学研究センター](https://ccportal.ims.ac.jp/)


### 第一原理計算関係
- 主にPAWポテンシャルを使ったDFT計算ができる。有償。
[VASP](https://www.vasp.at/)
- フォノン関係の計算機能が充実していたり、いろいろなことができる。
[Quantum-Espresso](https://www.quantum-espresso.org/)
- 局在原子基底系を使うので計算コストが軽い。
[SIESTA](https://departments.icmab.es/leem/siesta/)
- 第一原理計算の結果を使ってWannier基底でのタイトバインディングハミルトニアンを作ったり、Wannier基底ベースで補間計算をしたりできる
[Wannier90](http://www.wannier.org/)


### その他計算パッケージ
- おそらく最もメジャーな古典分子動力学コードの一つ。[LAMMPS](https://lammps.sandia.gov/)
- 格子動力学法の計算に特化している。[GULP](https://gulp.curtin.edu.au/gulp/)
- 各種DFT計算と組み合わせてフォノン・フォノン物性を計算する代表的なコード。[phonopy](https://phonopy.github.io/phonopy/)

### Tools
- pythonのライブラリで各種構造ファイルから第一原理計算等のインプットを作ったり、計算結果を処理したりするモジュールや、python内部から各種計算コードを呼び出したりするインターフェースが含まれていてすごく便利。
[Atomic Simulation Environment](https://wiki.fysik.dtu.dk/ase/)
- ASEと似た機能が多いが、綺麗なバンド図を書いたり、処理部分が強い印象
[pymatgen](https://pymatgen.org/)
- cif形式のファイルを処理するのに便利。表面を切り出したりするときに強力。
[cif2cell](https://sourceforge.net/projects/cif2cell/)
- バルク物性を網羅したデータベース。VASP用の構造ファイルもダウンロードできて便利。
[Materials Project](https://materialsproject.org/)
- 計算材料系の人間はだいたいこれを使って描画している気がする。綺麗な構造モデルが描画されるだけでなく、構造解析の機能とかもあって強い。
[VESTA](https://jp-minerals.org/vesta/jp/)
- 一部のアウトプットがこれじゃないと処理できない時がある。Fermi面とか描くときによく使う。
[xcrysden](http://www.xcrysden.org/)
- 第一原理計算とかのややこしいルーチンワークを自動化してくれる超強力pythonライブラリ。
[AiiDA](https://www.aiida.net/)




