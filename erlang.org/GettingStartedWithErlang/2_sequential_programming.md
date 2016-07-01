# 2 逐次プログラミング

## 2.1 Erlang のシェル

ほとんどの OS にはコマンドインタープリタかシェルがあり、Unix と Linux にはたくさん、Windows にはコマンドプロンプトがあります。Erlang には、ちょっとした Erlang のコードを直接書くことができて、何が起こるか(STDLIB マニュアルのページ [shell(3)](http://erlang.org/doc/man/shell.html)を参照してください)を確かめるためにそれらを評価(実行)することができる、専用のシェルがあります。

お使いの OS にあるシェル化コマンドインタープリタを立ち上げて、`erl` とタイプすることでErlang のシェルを(Linux か Unix で)開始させます。するとこのような感じのものが見られるでしょう。

```
% erl
Erlang R15B (erts-5.9.1) [source] [smp:8:8] [rq:8] [async-threads:0] [hipe] [kernel-poll:false]

Eshell V5.9.1  (abort with ^G)
1>
```

シェル上で "2 + 5." と打ち込み、エンター(キャリッジ・リターン)を入力してみましょう。終止符 "." とキャリッジ・リターンで終わることで、コードを入力し終わったよとシェルに伝えていることに気をつけましょう。

```
1> 2 + 5.
7
2>
```

ご覧のとおり、Erlang シェルは入力された行に番号を振り(1> 2> のように)、それから 2 + 5 が 7 であると正しく回答します。シェル上で入力を間違ってしまった場合、ほとんどのシェルのようにバックスペースキーで削除することができます。シェルにはさらに多くの編集用コマンドがあります(ERTS ユーザガイドにある ["tty - A command line interface"](http://erlang.org/doc/apps/erts/tty.html) を参照してください)。

(以下の例で、シェルから設定された多くの行番号が順番通りではないことに注意してください。これは、このチュートリアルがそれぞれのセッションで書かれ、コードがテストされたためです)

こちらは、より複雑な計算です:

```
2> (42 + 77) * 66 / 3.
2618.0
```

カッコの使用、乗算演算子 "\*"、そして除算演算子 "/"、これらは普通の四則演算です([Expressions](http://erlang.org/doc/reference_manual/expressions.html) を参照してください)。

Erlang システムと Erlang シェルをシャットダウンするには Control-C を入力します。

次のように出力されます:

```
BREAK: (a)bort (c)ontinue (p)roc info (i)nfo (l)oaded
       (v)ersion (k)ill (D)b-tables (d)istribution
a
%
```

"a" を入力すると Erlang システムを離れます。

Erlang システムを終了するもう1つの方法は、`halt()`と入力することです:

```
3> halt().
%
```

## 2.2 モジュールと関数

シェルからだけでしかコードを実行できないであれば、プログラミング言語はあまり役に立ちません。そこで、ここに小さな Erlang プログラムがあります。適したテキストエディタを使って`tut.erl`という名前のファイルにそれを入力します。`tut.erl`というファイル名は重要であり、また`erl`を開始したのと同じディレクトリにあります。もしあなたのエディタが入力やコードを良い感じにフォーマットするのをより簡単にしてくれる Erlang モード(ツールユーザガイドにある [The Erlang mode for Emacs](http://erlang.org/doc/apps/tools/erlang_mode_chapter.html) を参照してください)があるならラッキーですが、無くても完璧に上手くやれます。こちらが入力するコードです:

```erl
-module(tut).
-export([double/1]).

double(X) ->
    2 * X.
```

この「プログラム」が数値を倍にするのを推測するのは大変じゃありません。最初の2行は後で見てみることとしましょう。さあこのプログラムをコンパイルしてみてください。Erlang シェルでいかに表示するようにやってみましょう:

```
3> c(tut).
{ok,tut}
```

`{ok,tut}`はコンパイルが成功したということです。もし "error" と言われたら、入力したテキストに何か間違いがあるということです。追加のエラーメッセージが何が悪かったのかを教えてくれるので、書いた内容を変更してもう一度プログラムをコンパイルしてみることができます。

では、プログラムを実行してみましょう。

```
4> tut:double(10).
20
```

期待したとおり、10の倍は20です。

それでは、最初に2行に戻ってみましょう。Erlang のプログラムはファイル内に書かれます。それぞれのファイルは Erlang *モジュール* を含みます。モジュールにあるコードの最初の行はモジュールの名前です(["Modules"](http://erlang.org/doc/reference_manual/modules.html) を参照してください):

```erl
-module(tut).
```

なので、このモジュールは *tut* と呼ばれます。行末の終止符 "." に注意してください。モジュールを保存するために使われるファイルは、拡張子の ".erl" を除いてモジュールと同じ名前でなければなりません。今回のケースでは、ファイル名は`tut.erl`です。別のモジュールにある関数を使う時は、`module_name:function_name(arguments)`という文法を使います。ですから以下は`tut`モジュールにある`double`関数を引数 "10" で呼び出しているということです。

```
4> tut:double(10).
```

2行目は`tut`モジュールが`double`という関数を持っており、それは1つの引数(私たちの例では`X`)を取るということです:

```erl
-export([double/1]).
```

2行目はこの関数が`tut`モジュールの外から呼ぶこともできるということも表しています。これについては、あとでさらに説明します。繰り返しになりますが、行末の"."に注意してくださいね。

さて、もっと複雑な例として、数の階乗です。例えば、4の階乗は 4 * 3 * 2 * 1 で、これは 24 と等しくなります。

以下のコードを`tut1.erl`という名前のファイルに入力してみましょう:

```erl
-module(tut1).
-export([fac/1]).

fac(1) ->
    1;
fac(N) ->
    N * fac(N - 1).
```

なのでこれはモジュールで`tut1`と呼ばれ、`fac`という名前の関数を有しており、この関数は1つの引数`N`を取ります。

最初の部分は、1の階乗は1だということを表しています:

```erl
fac(1) ->
    1;
```

この部分はまだ`fac`の続きがあるということを指し示す";"で終端していることに注意してください。

2番目の部分はNの階乗はNにN-1の階乗が掛けられたものである、ということです。

```erl
fac(N) ->
    N * fac(N - 1).
```

この部分はこの関数にこれ以降の部分がないことを表す"."で終わっていることに気をつけましょう。

ファイルをコンパイルします:

```
5> c(tut1).
{ok,tut1}
```

そして4の階乗を計算してみましょう。

```
6> tut1:fac(4).
24
```

`tut1`モジュールの`fac`関数は引数`4`で呼び出されました。

関数はたくさんの引数を取ることが出来ます。2つの数字を掛け算する関数で`tut1`モジュールを拡張してみましょう:

```erl
-module(tut1).
-export([fac/1, mult/2]).

fac(1) ->
    1;
fac(N) ->
    N * fac(N - 1).

mult(X, Y) ->
    X * Y.
```

別の2引数関数`mult`があるという情報を`-export`行に追加する必要もあることに気をつけましょう。

コンパイルして:

```
7> c(tut1).
{ok,tut1}
```

新しい関数`mult`を試してみましょう:

```
8> tut1:mult(3,4).
12
```

この例では数値は整数で、`N`、`X`および`Y`というコード内の関数にある引数は変数と呼ばれます。変数は大文字で始めなければなりません([Variables](http://erlang.org/doc/reference_manual/expressions.html)を参照してください)。変数の例は`Number`、`ShoeSize`、そして`Age`です。

## 2.3 アトム

アトムは Erlang におけるもう一つのデータ型です。アトムは小文字から始まります([Atom](http://erlang.org/doc/reference_manual/data_types.html) を参照してください)、例えば: `charles`、`centimeter`、`inch` です。アトムは単純に名前であって、他の何者でもありません。変数のように値を持てるものでもありません。

`tut2.erl` という名前のファイルに次のプログラムを打ち込んでください。インチからセンチメートルに、そしてその逆に変換できたら便利でしょう:

```erl
-module(tut2).
-export([convert/2]).

convert(M, inch) ->
    M / 2.54;

convert(N, centimeter) ->
    N * 2.54.
```

コンパイルしましょう:

```
9> c(tut2).
{ok,tut2}
```

テストします:

```
10> tut2:convert(3, inch).
1.1811023622047243
11> tut2:convert(7, centimeter).
17.78
```

何の説明もなく小数(浮動小数点数)を導入しました。皆さんならきっと上手く切り抜けられるでしょう。

`centimeter` や `inch` 以外のものが `convert` 関数に入ってきたらどうなるか見てみましょう。

```
12> tut2:convert(3, miles).
** exception error: no function clause matching tut2:convert(3,miles) (tut2.erl, line 4)
```

`convert` 関数の2つの部分は節といいます。ご覧の通り、`miles` は節のどちらの部分でもありません。Erlang システムは節のどちらにも **マッチ** できないので、`function_clause` エラーメッセージが返されます。シェルはエラーメッセージを良い感じに整形しますが、エラーの組はシェルの履歴リストに保存され、`v/1` シェルコマンドによって出力することができます:

```
13> v(12).
{'EXIT',{function_clause,[{tut2,convert,
                                [3,miles],
                                [{file,"tut2.erl"},{line,4}]},
                          {erl_eval,do_apply,5,[{file,"erl_eval.erl"},{line,482}]},
                          {shell,exprs,7,[{file,"shell.erl"},{line,666}]},
                          {shell,eval_exprs,7,[{file,"shell.erl"},{line,621}]},
                          {shell,eval_loop,3,[{file,"shell.erl"},{line,606}]}]}}
```

## 2.4 タプル

さて、`tut2`のプログラムはちっとも良いプログラミングスタイルではありません。考えてみてください:

```erl
tut2:convert(3, inch).
```

これは3がインチを表しているのでしょうか？それとも、3はセンチメートルで、インチに変換したいのでしょうか？Erlangには物事をより理解できるようにするために、物事をまとめる方法があります。それを **タプル** と言い、中括弧"{"と"}"に囲まれています。

なので、`{inch,3}`は3インチを表し、`{centimeter,5}`は5センチメートルを表します。それではセンチメートルをインチに変換したり、その逆のことをしたりする新しいプログラムを書いてみましょう。次のコードを`tut3.erl`ファイルに入力してください:

```erl
-module(tut3).
-export([convert_length/1]).

convert_length({centimeter, X}) ->
    {inch, X / 2.54};
convert_length({inch, Y}) ->
    {centimeter, Y * 2.54}.
```

コンパイルしてテストしてみましょう:

```
14> c(tut3).
{ok,tut3}
15> tut3:convert_length({inch, 5}).
{centimeter,12.7}
16> tut3:convert_length(tut3:convert_length({inch, 5})).
{inch,5.0}
```

16行目で5インチをセンチメートルに変換し、それを戻してちゃんと元の値に戻りました。つまり、関数の引数に別の関数の結果を使うことが出来るのです。(上の)16行目がどのように動作しているのか考えてみましょう。関数に与えた引数`{inch,5}`はまず`convert_length`の最初の頭の節、つまり`convert_length({centimeter,X})`に対してマッチされます。`{centimeter,X}`は`{inch,5}`にマッチしないのが分かるでしょう(頭とは"->"の前の箇所です)。これに失敗したので、次の節の頭、つまり`convert_length({inch,Y})`を試してみましょう。これがマッチするので`Y`は値5を得ます。

タプルは2つよりも多い部分、実際欲しいだけの部分を持つことができ、どんな有効なErlangタームでも含めることが出来ます。例えば、世界の様々な都市の気温を表現するために

```erl
{moscow, {c, -10}}
{cape_town, {f, 70}}
{paris, {f, 28}}
```

と書くことが出来ます。

タプルは固定数のものを持ちます。タプル内のそれぞれのものを **要素** と呼びます。`{moscow,{c,-10}}`というタプルでは、要素1は`moscow`で要素2は`{c,-10}`です。ここで`c`はセ氏を、`f`はカ氏を表します。

## 2.5 リスト

タプルが物事をまとめるのに対して、物事のリストを表現できるようにもしたいです。Erlang におけるリストは "[" と "]" に囲まれています。例えば、世界中のいろんな都市の気温のリストは次のようになります:

```erl
[{moscow, {c, -10}}, {cape_town, {f, 70}}, {stockholm, {c, -4}},
 {paris, {f, 28}}, {london, {f, 36}}]
```

このリストはとても長かったので一行に収まりませんでした。これは問題なくて、Erlang はすべての「分別がある箇所」で改行ができますが、例えばアトムや整数などの途中ではできません。

リストの一部を見るためのとても便利な方法、それは "|" を使うことです。これはシェルを使った一番の例示です:

```text
17> [First |TheRest] = [1,2,3,4,5].
[1,2,3,4,5]
18> First.
1
19> TheRest.
[2,3,4,5]
```

リストの最初の要素と残りを分けるのに `|` が使われます。(`First` は値1を、 `TheRest` は値 [2,3,4,5] を得ます)

別の例:

```text
20> [E1, E2 | R] = [1,2,3,4,5,6,7].
[1,2,3,4,5,6,7]
21> E1.
1
22> E2.
2
23> R.
[3,4,5,6,7]
```

ここでは最初の2要素をリストから取得するのに `|` を使っています。もしリストにある要素より多くの要素を得ようとすれば、エラーが返ってきます。要素がない特別な場合のリストは [] です:

```text
24> [A, B | C] = [1, 2].
[1,2]
25> A.
1
26> B.
2
27> C.
[]
```

上記の例では新しい変数名を使っており、古いもの、つまり `First`、 `TheRest`、 `E1`、 `E2`、 `R`、 `A`、 `B`、 `C` を再利用しているものはありません。この理由は、変数はそのコンテキスト(スコープ)において一度だけ値を与えられうるものだからです。後でこの話に戻ってきます。

以下の例はどのようにリストの長さを求めるかを示します。下記コードを `tut4.erl` という名前のファイルに入力してみましょう:

```erl
-module(tut4).

-export([list_length/1]).

list_length([]) ->
    0;    
list_length([First | Rest]) ->
    1 + list_length(Rest).
```

コンパイルしてテストしてみます:

```text
28> c(tut4).
{ok,tut4}
29> tut4:list_length([1,2,3,4,5,6,7]).
7
```

説明しましょう:

```erl
list_length([]) ->
    0;
```

空のリストの長さは明らかに0です。

```erl
list_length([First | Rest]) ->
    1 + list_length(Rest).
```

最初の要素 `First` と残りの要素が `Rest` からなるリストの長さは 1 + `Rest` の長さになります。

(進んでいる読者の方へ: これは末尾再帰ではなく、この関数を書くためのもっと良い方法があります)

一般に、タプルは他の言語で言うところの「レコード」や「構造体」を使うような場面で使います。また、リストはサイズを変えられるような対象(つまり、他の言語で言うところの連結リストを使うような場面)を表したい時に使います。

Erlang は文字列データ型を持ちません。代わりに、文字列はユニコード文字のリストによって表されます。これは例えば、リスト `[97, 98, 99]` は "abc" と等しいということを示しています。Erlang シェルは「賢く」、どんな種類のリストを意図しているか推測し、シェルの考える最も適切な形式で出力します。例えば:

```text
30> [97,98,99].
"abc"
```

## 2.6 マップ

マップはキーから値への関連付けの集合です。これらの関連は "#{" と "}" の中に包まれています。 "key" から値42への関連を作るために、このように書きます:

```text
> #{ "key" => 42 }.
#{"key" => 42}
```

いくつかの興味深い特徴を使った例で、一番深いところに一気に飛び込みましょう。

以下の例は、色とアルファチャンネルの参照に対するマップを使って、どのようにアルファブレンディングを計算するかを示しています。`color.erl` という名前のファイルにコードを入力してみましょう:

```erl
-module(color).

-export([new/4, blend/2]).

-define(is_channel(V), (is_float(V) andalso V >= 0.0 andalso V =< 1.0)).

new(R,G,B,A) when ?is_channel(R), ?is_channel(G),
                  ?is_channel(B), ?is_channel(A) ->
    #{red => R, green => G, blue => B, alpha => A}.

blend(Src,Dst) ->
    blend(Src,Dst,alpha(Src,Dst)).

blend(Src,Dst,Alpha) when Alpha > 0.0 ->
    Dst#{
        red   := red(Src,Dst) / Alpha,
        green := green(Src,Dst) / Alpha,
        blue  := blue(Src,Dst) / Alpha,
        alpha := Alpha
    };
blend(_,Dst,_) ->
    Dst#{
        red   := 0.0,
        green := 0.0,
        blue  := 0.0,
        alpha := 0.0
    }.

alpha(#{alpha := SA}, #{alpha := DA}) ->
    SA + DA*(1.0 - SA).

red(#{red := SV, alpha := SA}, #{red := DV, alpha := DA}) ->
    SV*SA + DV*DA*(1.0 - SA).
green(#{green := SV, alpha := SA}, #{green := DV, alpha := DA}) ->
    SV*SA + DV*DA*(1.0 - SA).
blue(#{blue := SV, alpha := SA}, #{blue := DV, alpha := DA}) ->
    SV*SA + DV*DA*(1.0 - SA).
```

コンパイルしてテストしましょう:

```text
> c(color).
{ok,color}
> C1 = color:new(0.3,0.4,0.5,1.0).
#{alpha => 1.0,blue => 0.5,green => 0.4,red => 0.3}
> C2 = color:new(1.0,0.8,0.1,0.3).
#{alpha => 0.3,blue => 0.1,green => 0.8,red => 1.0}
> color:blend(C1,C2).
#{alpha => 1.0,blue => 0.5,green => 0.4,red => 0.3}
> color:blend(C2,C1).
#{alpha => 1.0,blue => 0.38,green => 0.52,red => 0.51}
```

この例はいくつか説明が必要です:

```erl
-define(is_channel(V), (is_float(V) andalso V >= 0.0 andalso V =< 1.0)).
```

まず、ガードテストの助けになるように `is_channel` というマクロが定義されています。これはここだけで便利なものであり、構文で溢れかえるのを減らすためのものです。マクロについての詳細は、[The Preprocessor](http://erlang.org/doc/reference_manual/macros.html) を参照してください。

```erl
new(R,G,B,A) when ?is_channel(R), ?is_channel(G),
                  ?is_channel(B), ?is_channel(A) ->
    #{red => R, green => G, blue => B, alpha => A}.
```

`new/4` 関数は新しいマップのタームを作り、 `red`、 `green`、 `blue`、および `alpha` というキーに初期値を紐付けます。この場合、各引数に対して ?is_channel/1 マクロによって保証された0.0以上1.0以下の浮動小数点数のみが許可され、引数ごとに`?is_channel/1` マクロによって保証されます。新しいマップを作る際、`=>` 演算子のみが許可されます。

`new/4` で作成した色のタームに対して `blend/2` を呼び出すことにより、2つのマップのタームで決定される結果の色を計算することができます。

`blend/2` が行う最初のことは、結果のアルファチャンネルを計算することです:

```erl
alpha(#{alpha := SA}, #{alpha := DA}) ->
    SA + DA*(1.0 - SA).
```

`:=` 演算子を用いることで、両方の引数に対して `alpha` キーに関連付けられた値が取得されます。マップにある他のキーは無視され、`alpha` キーだけが必須となりチェックされます。

これはまた `red/2`、 `blue/2`、 `green/2` 関数の場合でも同様です。

```erl
red(#{red := SV, alpha := SA}, #{red := DV, alpha := DA}) ->
    SV*SA + DV*DA*(1.0 - SA).
```

ここでの違いは、各マップ引数にある2つのキーに対してチェックを行うということです。他のキーは無視されます。

最後に `blend/3` で結果の色を返します:

```erl
blend(Src,Dst,Alpha) when Alpha > 0.0 ->
    Dst#{
        red   := red(Src,Dst) / Alpha,
        green := green(Src,Dst) / Alpha,
        blue  := blue(Src,Dst) / Alpha,
        alpha := Alpha
    };
```

新しいチャンネル値で `Dst` マップが更新されます。既存のキーを新しい値で更新するための構文は := 演算子を使います。

## 2.7 標準モジュールとマニュアルのページ

Erlang には物事を行うのに便利なたくさんの標準モジュールがあります。例えば、`io` モジュールには整形された入出力を扱うのに便利なたくさんの関数が入っています。標準モジュールについての情報を調べるために、`erl -man` コマンドを OS のシェルやコマンドプロンプト(`erl` を起動したのと同じ場所)で使うことができます。OS のシェルコマンドを試してみましょう:

```text
% erl -man io
ERLANG MODULE DEFINITION                                    io(3)

MODULE
     io - Standard I/O Server Interface Functions

DESCRIPTION
     This module provides an  interface  to  standard  Erlang  IO
     servers. The output functions all return ok if they are suc-
     ...
```

もしお使いのシステムで上手く動作しない場合、ドキュメントが Erlang/OTP のリリース版に HTML として含まれています。www.erlang.se (商用 Erlang) や www.erlang.org (オープンソース) のいずれからでも、ドキュメントを HTML として読んだり PDF としてダウンロードしたりすることができます。例えば R9B リリースですと次のようになります:

```text
http://www.erlang.org/doc/r9b/doc/index.html
```

## 2.8 ターミナルへの出力を書く

例の中で整形して出力できるといいので、次の例は `io:format` 関数を使うシンプルな方法をお見せします。エクスポートされた他のすべての関数のように、`io:format` 関数をシェル上でテストすることができます:

```text
31> io:format("hello world~n", []).
hello world
ok
32> io:format("this outputs one Erlang term: ~w~n", [hello]).
this outputs one Erlang term: hello
ok
33> io:format("this outputs two Erlang terms: ~w~w~n", [hello, world]).
this outputs two Erlang terms: helloworld
ok
34> io:format("this outputs two Erlang terms: ~w ~w~n", [hello, world]).
this outputs two Erlang terms: hello world
ok
```

`format/2` (つまり、2引数の `format`)関数は、2つのリストを取ります。最初のものは、ほぼ必ず" "の間に書かれたリストです。このリストは、各 ~w が2番目のリストから順番通りに取得されたタームによって置き換えられるのを除いて、書いたとおりに印字されます。~n はそれぞれ改行に置き換わります。すべてが計画通りに進んだ場合、`io:format/2` 関数自身は `ok` アトムを返します。Erlang の他の関数のように、エラーが発生した場合はクラッシュします。これは Erlang においては失敗ではなく、よく考慮された上でのポリシーなのです。Erlang は後ほど目にするエラーを扱う洗練されたメカニズムを持ちます。練習として、`io:format` をクラッシュさせてみましょう、難しくはないはずです。ですが、`io:format` がクラッシュしても、Erlang シェル自身はクラッシュしないことが分かります。

(鋭意翻訳中)

----

Copyright (c) 1996-2016 Ericsson AB. All Rights Reserved.