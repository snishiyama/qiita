# はじめに

本記事は，「PsychoPy Coderによる心理学実験作成チュートリアル」の第3回の記事です。[第2回](https://qiita.com/snishym/items/31c980f08e62190469f9)ではfor文を利用した刺激の系列提示の方法を紹介しました。今回は，刺激の提示位置の変更方法およびちょっと進んだfor文の使い方について解説します。詳細は右側の見出しリストをご覧ください。

このチュートリアルシリーズの目的・概要等が気になった方はこちらの[全体のまとめ](https://qiita.com/snishym/items/8b52db0d901cf5744463)をご一読ください。

# 刺激の提示位置の変更方法

```python:change_position.py
from psychopy import visual, core

win = visual.Window()

letter_stim = visual.TextStim(win)

letter_stim.setText("L")
letter_stim.setPos([-0.3, 0]) # 中心から左端までの長さを1として0.3だけ位置を動かす
letter_stim.draw()
win.flip()
core.wait(1)

letter_stim.setText("R")
letter_stim.setPos([0.3, 0]) # 中心から右端までの長さを1として0.3だけ位置を動かす
letter_stim.draw()
win.flip()
core.wait(1)

win.close()
```

`.setPos()`という関数（メソッド）で刺激の提示位置を変更することができます。()内には，`[横方向, 縦方向]`のリストを入れます[^1]。

[^1]: 公式リファレンスではタプルを入れるように指定されていますが，リストでも動きます。タプルの説明が面倒だったので，リストを使用しています。なお，タプルの使い方はリストとだいたい同じです。気になる方はググってください。

PsychoPyにおいて`[0,0]`は画面の中央になります。デフォルトのウィンドウ設定では，横方向，縦方向ともに -1 ~ 1 の範囲で位置を指定できます。右（上）方向が正の値になります。中央から端までの長さを1として，その比率で位置を決定することができます[^2]。値を様々に変更してみるとその使い方が実感できると思います。

[^2]: cmやpix（ピクセル）などで提示位置を指定できるように設定を変更することも可能ですが，簡単のため，本シリーズではデフォルト設定の利用方法のみを紹介します。

```
                 [0,1]
                   ^
                   |
                   |
                   |
[-1,0]|----------[0,0]----------->[1,0]
                   |
                   |
                   |
                   -
                 [0,-1]
```

# リストのリスト

前回と同様，最初のコードをfor文で書きたいところです。しかし，前回と違って，文字と位置を同時に変更する必要があります。これを解決するために，リストのリストを使います。次節で，リストのリストを使ったfor文を紹介します。

といっても，リストのリストは単に，リストの要素にリストが含まれているものです。

```python:list_list.py
list_list = [["a","あ"],["i","い"],["u","う"]]
```

見やすさのために，以下のように書くこともあります。

```python:list_list2.py
list_list2 = [
    ["a","あ"],
    ["i","い"],
    ["u","う"]
]
```

サイモン課題のために，文字と位置のリストを作成します。今回のサイモン課題では横方向だけを変更するため，縦横両方の位置を指定するためのリストではなく，横方向の数値だけを要素にしています。

```python:list_letter_pos.py
letter_pos = [
    ["L",-0.3], # 一致
    ["R",0.3], # 一致
    ["L",0.3], # 不一致
    ["R",-0.3] # 不一致
]
```

これを前回紹介したfor文で出力してみます。

```python:simple_list_list_for.py
letter_pos = [
    ["L",-0.3], # 一致
    ["R",0.3], # 一致
    ["L",0.3], # 不一致
    ["R",-0.3] # 不一致
]

for i in letter_pos:
    print(i)
```

Coderの下部の出力にはリストが出力されたはずです。letter_posというリストの要素はリストなので，順番にリストが取り出されたということになります。取り出したのがリストのままでは`setText()`も`setPos()`もやりにくいです。次節でfor文の使い方を少しだけ発展させます。

# ちょっと進んだfor文

実は，for文の最初の行では以下のようにカンマ`,`区切りで複数の名前を指定できます。

```python:multivars_for.py
letter_pos = [
    ["L",-0.3],
    ["R",0.3]
]

for letter, x_axis in letter_pos:
    print(letter)
    print(x_axis)
```

出力エリアには，L(`letter`)，-0.3(`x_axis`)，R(`letter`)，0.3(`x_axis`)の順に出力されているはずです。つまり，for文でletter_posから取り出されたリスト内の2つの要素がそれぞれ順番に`letter`と`x_axis`に割り当てられたということです。

今回新たに作成したリストのリストに対して，for文のこの便利な機能[^3]を利用することで，前回のfor文を少しだけ変更するだけで，文字と位置の両方が順番に変化する系列提示を実現することができます。

[^3]: for文以外でも，リストの中身を複数の名前に同時に割り当てることは可能です。

```python:sequential_text_pos.py
from psychopy import visual, core

win = visual.Window()

letter_stim = visual.TextStim(win) 
letter_pos = [
    ["L",-0.3], # 一致
    ["R",0.3], # 一致
    ["L",0.3], # 不一致
    ["R",-0.3] # 不一致
]

for letter, x_axis in letter_pos: # 2つの要素をそれぞれletterとx_axisに割り当てる
    letter_stim.setText(letter)
    letter_stim.setPos([x_axis, 0]) # 刺激の提示位置を変更する
    letter_stim.draw()
    win.flip()
    core.wait(1)

win.close()
```

# おわりに

今回は，刺激に加えて提示位置も同時に変更しながら系列提示する方法を紹介しました。そのために，リストのリストを使い，それをうまく活用するためのfor文の書き方を少し変更しました。

次回はキー反応の取得を行います。これでサイモン課題のプログラムは一旦完成します。引き続きがんばりましょう！

[【第4回】キー反応の取得・刺激のランダマイズ](https://qiita.com/snishym/items/0b10e714363656094181)