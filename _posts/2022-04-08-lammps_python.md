---
layout: post
title: LAMMPSを実行できるpython環境の作成
summary: |-
    　毎回手こずるので備忘録
author: Emi Minamitani
---

LAMMPSというのは固体系の古典分子動力学法のデファクトスタンダードみたいなツールである。C++で書かれているのだが、インプットファイルの書きかたが「LAMMPS語」みたいな、割と特殊な構造をしている。

シミュレーションでは、たとえばなんかのパラメータを変えながら100回計算を繰り返すとかをしたいことが多くて、その場合loopを書くことになるのだが、
「LAMMPS語」でループを書くのが結構めんどくさい。
あとLAMMPSの機能だけであれこれインプット構造とかを書くのが面倒なので、
できれば手慣れたpython+ASEとかを使いたい…というのはしょっちゅう思う。

それを可能にするのが、LAMMPSのpythonインターフェースを導入することだ。
最近はこればっかりつかっている。ただ、導入のときにはちょいちょい罠があって、
毎回手こずるので、メモを残しておこうと思う。

## 仮想環境の作成
システムワイドに入れるのは失敗したときに厄介なので、仮想環境を作っておく。
この仮想環境はvirtualenvで作り、pyenvなどは使わないようにしておく。
（なぜだかわからないが、pyenvで仮想環境を管理してると失敗する。)

```
python -m venv .venv
source ./venv/bin/activate
```

```
pip install virtualenv
```

この仮想環境のPATHが`$HOME/LAMMPS/.venv`だとしておく。

## LAMMPSコンパイル

githubから落としてくる。
使う仮想環境のパスを設定して、cmakeのbuildの準備をする。
```
git clone -b release https://github.com/lammps/lammps.git lammps-python
cd lammps-python
export VIRTUAL_ENV=$HOME/LAMMPS/.venv
mkdir build
cd build
```
intel のicpcとかを使う＆最小限の構成は入れておく場合の設定。（あれこれ試したのでごちゃごちゃしている。`-D PKG_MOLECULE=on`とかのオプションは、たぶんいくつかダブっていて実際は入れなくていいのも入っていると思う。）
```
cmake -C ../cmake/presets/intel.cmake -C ../cmake/presets/basic.cmake  -D CMAKE_INSTALL_PREFIX=$VIRTUAL_ENV -D PKG_MOLECULE=on -D WITH_PNG=yes -D WITH_JPEG=yes  -D BUILD_SHARED_LIBS=on -D PKG_MPI=on -D LAMMPS_EXCEPTIONS=on -D PKG_PYTHON=on -D PKG_MANYBODY=on -D PKG_MPIIO=on -D PKG_PHONON=on  ../cmake
```

このときに、pythonとして指定した環境のものを認識しているかを確認すること。コンソールの出力にコンパイラとかの情報と一緒にどのpythonを参照しているかのメッセージも含まれている。

```
cmake --build .
cmake --install .
echo `export LD_LIBRARY_PATH=$VIRTUAL_ENV/lib:$LD_LIBRARY_PATH` >> $VIRTUAL_ENV/bin/activate
make install-python
```

buildしたときにliblammps.soができていなかったら、何かがおかしい。
（ひっそりうまく行かなくてpythonとの連携がうまく行かないことが多々ある。）
最後までうまくいくと、
`$VIRTUAL_ENV/lib64/python3.6/site-packages/lammps`下にliblammps.soとともに、pythonのインターフェース一式が配置される。

仮想環境を起動して、そのpythonを使って
```
from lammps import lammps
lmp=lammps()
```
が動けばひとまずインターフェースは認識されている。

## サンプル

Lennard-Jonesでmelt-quenchとかはこんな感じ。
（cooling rateとかはテスト用なので適当。）
せっかくpythonから呼んでいるので、今度からはTensorBoardとかWandbとかでログ取ったりしよう。

```
from lammps import PyLammps
lmp=PyLammps()

lmp.command('units		lj')
lmp.command('atom_style	atomic')

lmp.command('boundary		p p p')
lmp.command('lattice		fcc 1.073')
lmp.command('region box block 0 6 0 6 0 6')
lmp.command('create_box 1 box')
lmp.command('create_atoms 1 box')
lmp.command('variable rc equal {0:f}'.format(2.5))

lmp.command('neighbor 0.3 bin')
lmp.command('neigh_modify	every 10 delay 0 check yes')

lmp.command('pair_style      lj/cut ${rc}')
lmp.command('pair_coeff      1 1 {0:f} {1:f}'.format(1.0,1.0) +' ${rc}')
lmp.command('mass 1  1.0')

lmp.command('variable	 dt equal 0.02')
lmp.command('variable	 tau_T equal 1e2*${dt}')
lmp.command('variable	 Tsta equal 0.01')
lmp.command('variable	 Tend equal 0.85')
lmp.command('variable	 run_melt equal 1000/${dt}')
lmp.command('variable	 run_mq equal 100/${dt}')
lmp.command('variable	 run_therm equal 1000/${dt}')
lmp.command('variable	 tau_P equal 1e3*${dt}')
lmp.command('variable	etol equal 0.0')
lmp.command('variable	ftol equal 1.0e-9')
lmp.command('variable	maxiter equal 5000')
lmp.command('variable	maxeval equal 8000')


lmp.command('thermo 1000')
lmp.command(
        'thermo_style custom step temp pe press pxx pyy pzz lx ly lz vol')
lmp.command('compute peratom all pe/atom')

lmp.command('velocity	all create 0.01 87287')
lmp.command('dump 1 all atom 1000 melt.dump')
lmp.command('fix		 buff all npt temp ${Tsta} ${Tsta} ${tau_T} iso 1.0 1.0 ${tau_P}')
lmp.command('run		 ${run_therm}')
lmp.command('unfix buff')
lmp.command('fix		 melt all npt temp ${Tsta} ${Tend} ${tau_T} iso 1.0 1.0 ${tau_P} ')
lmp.command('run		 ${run_melt}')
lmp.command('unfix melt')
lmp.command('fix		 buff all npt temp ${Tend} ${Tend} ${tau_T} iso 1.0 1.0 ${tau_P}')
lmp.command('run		 ${run_therm}')
lmp.command('unfix buff')
lmp.command('fix		 quench all npt temp ${Tend} ${Tsta} ${tau_T} iso 1.0 1.0 ${tau_P}')
lmp.command('run		 ${run_mq}')

```
