# Perl6入学式

---
## Perl6入学式の目的
* Perl5が使える人にPerl6を理解してもらう
* Perl6の言語仕様の面白さを理解してもらう
* クリスマスの祭りに備える 

---
## Perl6とは

* Larry Wallが中心となって開発している新しいプログラミング言語
* 元々Perl5の次のバージョンとして開発していた
* 現在はPerl6という独立した言語として開発
* Perl5との互換性はない
* Perl5をホビット物語とするとPerl6はロードオブザリング

---
## Perl6の特徴

* 完全なオブジェクト指向言語
* 再設計された正規表現
* 並列処理が言語組み込み

---
## Perl6の処理系

過去には複数の処理系があった

* rakudo(NQP)
* Niecza(.NET)
* Pugs(Haskell)

---

## rakudoのインストール

$HOME/perl6へインストール

```
$ git clone https://github.com/rakudo/rakudo.git 
$ cd rakudo
$ perl Configure.pl --prefix=$HOME/perl6 --gen-moar \
    --gen-nqp --backends=moar
$ make
$ make install
```

---
## Hello, Perl6

perl6をREPLで起動

```
$ perl6
> say 'Hello, Perl6';
Hello, Perl6
```

perl6コマンドでファイルから実行

```
$ echo "say 'Hello, Perl6';" > hello.pl
$ perl6 hello.pl 
```

---
## 基本的なデータ型 

* 数値や文字列はPerl5と同じように扱える
* 文字列連結は```.```から```~```に変更

```
my $a = 1;
my $b = 1.23;

my $c = 'Perl6';
my $d = 'Perl5';

# 文字列の連結
say $c ~ $d;  # Perl6Perl5
```

---
## 全てがオブジェクト

* 数値や文字列に対してメソッドを呼び出せる
* メソッド呼び出しは`.`を使う
* サブルーチンスタイルの呼び出しも可能

```
1.say;       # 1
say 1;

3.14.say;    # 3.14
say 3.14;

'Perl'.say;  # Perl
say 'Perl';
```

---
## オブジェクトの型を知る

* ```WHAT```メソッドで型を知ることができる

```
my $a = 1;
say $a.WHAT;     # (Int) 整数オブジェクト 

my $b = 3.14;
say $b.WHAT;     # (Rat) 有理数オブジェクト

my $c = 'Perl';
say $c.WHAT;     # (Str) 文字列オブジェクト 
```

---
## Perl6における型

* 全ての値は何らかのクラスに属する 

---
## 標準クラス(一部)

|クラス|説明|
|---|---|
|Any|新しく定義されたクラスのベースクラス 
|Mu|全てのクラスのルートクラス 
|Str|文字列を表すクラス
|Array|配列を表すクラス
|Hash|ハッシュを表すクラス
|Code|サブルーチンなどのコードを表すクラス
|Int|整数を表すクラス
|Match|正規表現でマッチした結果を表すクラス

---
## Strクラスのメソッド 

```
# 文字列の長さを取得(lengthメソッドはない)
my $c = 'Perl';
$c.chars;        # 4
chars $c

$c.substr(0, 1);  # P
substr $c, 0, 1;

my $d = 'a_b_c_d';
$d.split(/_/);    # a b c d
split $d, /_/;

my $e = "a\nb";
$e.lines;         # a b 
lines $e;
```

---
## 配列

* ```@array```で配列を宣言
* アクセス方法がPerl5と異なる
* ```@array[0]```で0番目の要素にアクセス

```
# リストを代入
my @a = (1, 2, 3);

# 配列の要素数を取得
say @a.elems;      # 3

# 0番目の要素へのアクセス
say @a[0];         # 1

# 4を追加 
@a.push(4);

# perlメソッドで要素をダンプ
say @a.perl;       # [1, 2, 3, 4] 

# -で要素を連結
say @a.join('-');  # 1-2-3-4

# 最後の要素を取得
say @a.pop;        # 4

# 先頭の要素を取得 
say @a.shift;      # 1

my @b = 1, 2, 3;   # ()なしのスタイル
say @b.elems       # 3

# map
@b.map({ say $_ });  # 1, 2, 3
@b.map: { say $_ };  # 1, 2, 3

map { say $_ }, @b;  # 1, 2, 3
```

---

## perlメソッド

* Perl6では、全てのオブジェクトに```perl```メソッドが実装されている
* 複雑なオブジェクトのダンプに使う
* Perl5でいう```Data::Dumper```

```
my @a = (1, 2, 3);
say @a.perl;       # [1, 2, 3]
```

---
## Perl6におけるリスト

* Perl6では、リストは`List`オブジェクトとして表現される
* Perl5と違い、リストの中にリストを書いても自動的に展開しない

```
# スカラー変数への代入は展開しない
my $a = (1, (2, (3, 4)));
say $a.WHAT;   # (List)

$a[1].perl;    # (2, (3, 4))

# 配列への代入でも展開しないがListからArrayになる
my @a = (1, (2, (3, 4)));
say @a.WHAT;      # (Array)
say @a[1].perl;   # 2
```

---
## pushとappendの違い

* `push`は配列に1つの要素を追加する(追加する要素が配列の場合は展開されない)
* `append`は配列に複数の要素を追加する(追加する要素が配列の場合は展開される)
* Perl5のように`push`は要素を自動的に展開しない

```
my @a = (1,2,3);
my @array;

@array.push: @a;
@array.elems;      # 1

my @b = (4,5,6);
@array.append: @a;
@array.elems;      # 4
@array.perl;       # [[1,2,3], 1, 2, 3]
```


---
## ハッシュ

* `%hash`でハッシュを宣言
* アクセス方法がPerl5と異なる
* `%array<a>`でキーが`a`の要素にアクセス
* `%array{"a"}`でキーが`a`の要素にアクセス
* `{}`を使う場合はキーを`"`で囲む必要がある
---
## ハッシュ

```
# ハッシュへの代入
my %a = (a => 1, b => 2);

# ハッシュの要素数を取得
say %a.elems;      # 2

# 0番目の要素へのアクセス
say %a<a>;         # 1
say %a{"a"};       # 1

# perlメソッドで要素をダンプ
say %a.perl;       # {:a(1), :b(2)}<>  

# キーを取得
say %a.keys;       # a b

# 値を取得
my @values = %a.values;
say @values;       # 1 2

# kvメソッド(key-valueをまとめて配列にする)
my @kv = %a.kv;   
say @kv;           # a 1 b 2

```

---
## 型制約

* 変数に対して型を制約する
* 文字列しか受け付けないようなスカラー変数や整数しか代入できない配列などが定義できる

```
my Str $a = "Perl";  # 文字列しか代入できない
my Int $b = 1;       # 整数しか代入できない

$a = 1;       # コンパイルエラー
$b = 'Perl';  # コンパイルエラー

# Int型の配列
my Int @a = 1, 3, "Perl";     # コンパイルエラー
my Int @b = 1, 2, 3;          # OK

# Str型の配列
my Str @c = 1, 3, "Perl";     # コンパイルエラー
my Str @d = 'Python', 'Ruby', "Perl";  # OK
```

---
## 制御構造

* if文やwhile文はPerl5とほとんど同じ
* ifやwhileの後の()を書く必要がなくなった
* for文は、配列要素を左に書いて->後にループ変数を書く

```
my $a = True;

# if文
if $a {
    say $a;
} elsif {
    say 'elsif';
} else {
    say 'else';
}

# while文
my $str = "Hello, world";
my $end = $str.chars;
my $start = 0;
while $start < $end {
    my $c = $str.substr($start++, 1);
    say $c;
}

# for文
my @a = (1, 2, 3, 4);
for @a -> $a {
    say $a;   # $aに1, 2, 3, 4が入る
}

# for文
for 1, 2, 3, 4 {
    say $_;   # $_に1, 2, 3, 4が入る
    .say;     # $_なしでsayを呼ぶと$_が暗黙的に補完される
}

# 2つのループ変数
# 1回目に($i,$j)=(1,2)、2回目に($i,$j)=(3,4)が入る 
for 1, 2, 3, 4 -> $i, $j {
    say "$i, $j";
}

# レンジ指定
for 1..4 {
    .say;
}

# ハッシュをループ
# 1回目に($i,$j)=('a',1)、2回目に($i,$j)=('b',2)が入る 
my %b = (a => 1, b => 2);
for %b.kv -> $i, $j {
    say "$i $j";
}

```

---
## Bool値

* Bool型が追加
* True,False

```
# True Value
my $a = True;
say $a.WHAT;      # Bool   

# False Value
my $b = False;
say $b.WHAT;      # Bool   
```
---
## undef値はない

* Perl5のundefはPerl6ではない
* definedメソッドの結果がFalseを返すかどうかで判断

```
my $a;
say $a.defined; # False

if $a.defined {} else { say 'undefined' }
if True  { say 'True' } 
if False { } else { say 'False' }
```

---
## サブルーチンの定義

* サブルーチンの仮引数を宣言することができる
* @_から取り出す必要はない(取り出すことも可能)
* 宣言したサブルーチンに引数がある場合、引数を省略して呼び出すことはできない
* 仮引数の宣言部分をSignatureと呼ぶ
* Signatureもオブジェクト


---
## サブルーチンの定義
```
# サブルーチンの引数を宣言できる
sub foo ($a) {
    say $a;
}

# 引数に1を渡す
foo(1);   # 1

# 引数なしで呼び出すとエラー
foo();

# Signatureオブジェクト
# 宣言部の前に:をつけるとSignatureオブジェクトになる
say :($a).WHAT;  # (Signature)

# 引数の数
say :($a).arity;     # 1

# パラメータ
say :($a).params;    # Mu $a 
```

---
## 名前付き引数

* (:$a, :$b)
* :のあとに変数名を書く

```
# 名前付き引数
# a => 1と渡された場合は、$aに1が設定されている
# :a(1)と渡された場合は、$aが1に設定される
# :$aと渡された場合は、$aに1が設定される
sub bar (:$a, :$b) {
    say $a;    # 1
    say $b;    # 2
}

# ハッシュのように指定する
bar(a => 1, b => 2);

# 名前付き引数
bar(:a(1), :b(2));

my $a = 1;
my $b = 2;

# 変数を名前付き引数で渡す
foo(:$a, :$b);
```

---
## Slurpy引数

* 引数を1つの配列やハッシュにまとめて受け取れる記法
* Slurpy引数は、配列やハッシュ変数の前に*をつける

```
# Slurpy引数(ハッシュで受け取る)
sub foo (*%args) {
    say %args<a>;   # 1
    say %args<b>;   # 2
}

# ハッシュのように指定する
# 名前付き引数と同様に扱われる
foo(a => 1, b => 2);

# 名前付き引数
foo(:a(1), :b(2));

my $a = 1;
my $b = 2;

# 変数を名前付き引数で渡す
foo(:$a, :$b);

# これは名前付き引数ではないのでエラー
foo(1, 2, 3);

# Slurpy引数(配列で受け取る)
sub bar (*@args) {
    say @args[0];  # 1
    say @args[1];  # 2
}

bar(1, 2, 3);

# これは名前付き引数なので、エラー
bar(a => 1, b => 2);

# 最初の引数が$aに代入されて、残りが名前付き引数として%argsに代入される
sub baz ($a, *%args) {
    say $a;             # 1 
    say %args.perl;     # {:a(1), :b(2)}
}

baz(1, a => 1, b => 2);
```

---
## デフォルト引数

* 引数にデフォルト値を指定できる

```
# デフォルト引数
sub foo ($a = 1) {
    say $a;
    say @args.perl;
}

# 引数を指定しない場合はデフォルト引数の値が使われる
foo();   
```

---
## `|`演算子 

* `@a`をサブルーチンに渡してもPerl5のように個別の引数に展開されない
* `|@a`を渡すと個別に引数を展開する

```
sub foo($a, $b) {
    say $a;
    say $b;
}

my @a = (1,2);

foo(@a);      # コンパイルエラー
foo(|@a);     # 1 2
```

---
## サブルーチンでの型制約

* 引数にも型制約をつけることができる

```
# Strによる制約
sub foo(Str $a) {
    say $a;
}

foo(1);      # エラー
foo(3.24);   # エラー
foo('foo');  # OK 

my Int $i = 0;
my Str $s = 'Perl';
foo($i);     # エラー
foo($s)      # OK;
```

---
## definedな値のみ渡せる制約

```
# Strの後に:Dと書く(Defineの略)とundefineな値が渡された場合、エラー
sub foo(Str:D $a) { }

# Strの後に:Uと書く(Undefineの略)とundefineな値が渡されてもOK
sub bar(Str:U $a) { }

my Str $a;
foo($a);   # エラー
bar($a);   # OK
```

---
#演算子
---
## ==演算子

* ==演算子は両辺を数値に変換して比較する
* 例えば、配列を==で比較すると配列の要素数を比較する

```
my @a = (1, 2);
my @b = (2, 4);

# 両辺を数値に変換して比較
# この場合、配列の要素数を比較
say @a == @b;     # True

my %d = (a => 1); 
my %e = (b => 1);

say %d == %e;     # True

```

---
## ===演算子

* ===演算子は両辺が完全に同じオブジェクトだった場合、真を返す

```
my @c = (1, 2);
my @d = (1, 2);

say @c === @d;         # False
say (1,2) === (1,2);   # True

say 1 === 1;      # True
say 'a' === 'b';  # False
```

---
## eq演算子

* `eq`演算子は両辺を文字列に変換してから比較する

```
my $a = 1;
my $b = 1;

say $a eq $b;  # True 
  
```

---
## eqv演算子

* `eqv`演算子は両辺の値を比較する
* 配列やハッシュに適用すると要素の値を再帰的に比較する

```
my @a = (1, 2);
my @b = (2, 4);

# 値を比較
say @a eqv @b;    # False

my @c = (1, 2);
my @d = (1, 2);

say @c eqv @d;    # True

my %d = (a => 1); 
my %e = (b => 1);

say %d eqv %e;    # False

```

---

## クラス
* `class`キーワードでクラスを定義
* `method`キーワードでメソッドを定義
* `new`メソッドが自動で追加
---

## クラス属性
* `has`キーワードで属性を定義
* twigilが`!`の場合はプライベートな属性
* twigilが`.`の場合はパブリックな属性
* パブリックな属性はアクセッサが自動で生成される
* `is`で属性の特徴を決める
    * `rw` 読み書き可能
    * `ro` 読み込みのみ可能

---

## クラス定義の例

```
class Emperor {

    has Int $.hp is rw = 500;
    has Int $.mp is rw = 100;
    has Int $.attack is rw = 200;

    method final_strike { "final strike"; }
    method bull_crash   { "bull crash";   }
    method tomahawk     { "tomahawk";     }
    method sky_drive    { "sky drive";    }

    method get_parameters {
        my $params = {};
        for self.^attributes -> $a {
            $params.{$a.name} = $a.get_value(self);
        }
        return $params;
    }
}

my $emperor = Emperor.new;
$emperor.hp = 1000;
$emperor.mp = 5000;   
$emperor.attack = 300;

say $emperor.get_parameters;
```

--- 

## self
* メソッド内で使用できる
* 自身を呼び出したオブジェクトを参照
* `$.a`という属性は、`self.a`で参照できる

---

## selfの例
```
class Emperor {
    has Int $.hp is rw = 500;
    method dump {
        say self\.hp;
    }
}

my $emperor = Emperor.new;
$emperor.dump;    # 500
```

---
## サブメソッド

* `submethod`キーワードでサブメソッドを定義
* クラス生成に関連するメソッド
* オーバーライドできない
* `BUILD`メソッドでプライベートな属性を初期化

---
## サブメソッドの例
```
class Music {
    has $!title;       # $!を使うとプライベートなアトリビュートを定義できる
    has $!composer;     

    # submethodはクラスの基盤となるメソッドのこと
    # オーバーライドできない
    # 名前付き引数で宣言すると.newでオブジェクトを生成した時に自動的に呼び出される
    submethod BUILD (:$!title, :$!composer) {
      # 中身はなくてもよい
      # 追加で初期化した処理があれば書く
    }
    method get_title {
        $!title;
    }
}

# newでオブジェクト生成
# titleをハッシュで渡す
my $music = Music.new(title => 'Shine!!');

# Shine!!が表示される
say $music.get_title;

```

---
## 継承 

* `is`キーワードで継承できる 
* `A is B`で`B`を継承したAクラスを定義

```
class Wizard is Emperor {

    # 魔法使いの必殺技
    method fire      { say "fire";      }
    method blizzard  { say "blizzard";  }
    method thunder   { say "thunder";   }
    method cure      { say "cure";      }
}

# Wizardオブジェクトを作成する
my $wizard = Wizard.new(
    hp => 200,
    mp => 1000,
    attack => 50 
);

# Wizardクラスのメソッド
$wizard.fire;       # fire        
$wizard.blizzard;   # blizzard
$wizard.thunder;    # thunder
$wizard.cure;       # cure

# Emperorメソッドを呼び出す
say $wizard.hp;     # 200 
say $wizard.mp;     # 1000
say $wizard.attack; # 50
```

---
## 継承したクラスの型制約

* 制約に書いたクラスを継承したクラスは代入できる

```
class Wizard is Emperor { ... }

my Emperor $emperor = Wizard.new;    # OK
```

---
## Role

* `role`キーワードで定義
* `A does B`で`role B`を`class A`に組み込む
* ScalaのTraitやRubyでいうMixinに近い

---
## Roleの例
```
role Wizard {
    method fire      { say "fire";      }
    method blizzard  { say "blizzard";  }
    method thunder   { say "thunder";   }
    method cure      { say "cure";      }
}

class Emperor does Wizard {

    has Int $.hp is rw = 500;
    has Int $.mp is rw= 100;
    has Int $.attack is rw = 200;

    method final_strike { "final strike"; }
    method bull_crash   { "bull crash";   }
    method tomahawk     { "tomahawk";     }
    method sky_drive    { "sky drive";    }

    method get_parameters {
        my $params = {};

        for self.^attributes -> $a {
            $params.{$a.name} = $a.get_value(self);
        }
        return $params;
    }
}

my $emperor = Emperor.new(
    hp => 200,
    mp => 1000,
    attack => 50 
);

# Wizard Roleで定義したメソッドが使える
$emperor.fire;       # fire        
$emperor.blizzard;   # blizzard
$emperor.thunder;    # thunder
$emperor.cure;       # cure

# Roleは、classとしても扱える
my $wizard = Wizard.new;

$wizard.fire;
$wizard.blizzard;   # blizzard
$wizard.thunder;    # thunder
$wizard.cure;       # cure

```

---
## 正規表現

* パターンマッチ演算子には`~~`を使う
* `.`が改行にもマッチ
* デフォルトでパターンの空白を無視する
* マッチの結果を`Match`オブジェクト(`$/`)として取得できる
* キャプチャーは`$0`から始まる
* 修飾は`m:i//`のように先頭に書く

---
## 正規表現

```
my $a = 'PerlPythonRuby';
if $a ~~ / (Perl) / {
    say $/.WHAT;   # Matchオブジェクト
    say $/.from;   # 0
    say $/.to;     # 4
    say $0;        # Perl
}
if $a ~~ m:i/ (perl) / {
    say $/.WHAT;   # Matchオブジェクト
    say $/.from;   # 0
    say $/.to;     # 4
    say $0;        # Perl
}
```

---
## 正規表現

* 空白に意味を持たせない場合は、パターンを`''`で囲む

```
my $a = 'Perl Ruby';
if $a ~~ / 'Perl Ruby' / {
    say $/.WHAT;   # Matchオブジェクト
}
```

---
## 文字クラス

* 文字クラスは、`<[]>`で指定する
* `<[a..z]>`

```
my $a = 'aaabbbccc';

# abcの中の1文字以上にマッチ
if $a ~~ / ( <[abc]>+ ) / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # aaabbbccc
}

```
---
## 文字クラス

* 文字クラスの否定は、`<-[]>`になる

```
my $a = 'http://aaabbbccc/';
if $a ~~ / ( 'http://' <-[\/]>+ '/' ) / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # http://aaabbbccc/
}
```

---
## 選択

* 選択のパターンは、`|`で指定する
* longest match

```
my $a = '++++';
if $a ~~ / ('+' | '++++') / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # ++++
}
```

---
## `^`

* `^`は必ず文字列の最初にマッチする
* Perl5でいう`\A`

```
my $a = 'Perl';
if $a ~~ / (^ Pe ) / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # Pe
}
if $a ~~ / (^ P ) / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # P
}
if $a ~~ / (^ Perl ) / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # Perl
}
```


---
## `^^`と^$$^

* `^^`は文字列の最初か改行の直後にマッチする
* `$$`は改行の前か文字列の最後が改行でなければ、文字列の末尾にマッチする
* 論理的な行にマッチする

```
my $a = "Perl\n";
if $a ~~ / (^^P ) / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # P
}
if $a ~~ / ( l$$ ) / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # l
}
```

---
## `[]`

* `[]`は、`()`と同じで正規表現をグルーピングするがキャプチャしない場合に使う

```
my $a = "Perl";
if $a ~~ / [Perl || Python || Ruby] / {
    say $/.WHAT;   # Matchオブジェクト
    say $0;        # Nil 
}
```

---
## キャプチャの順番

* キャプチャの順番は、`$0`から始まる

```
my $a = "Perl";
# / (Ruby) (Python) || (P) (.) (.) (.) / 
#     $0      $1        $0  $1  $3  $4
if $a ~~ / (Ruby) (Python) || (P) (.) (.) (.) / {
    say $/.WHAT;   # Matchオブジェクト
    say "$0 $1 $2 $3";   # P e r l
}
```

---
## 名前付き正規表現

* 正規表現に名前をつけることができる
* `regex`キーワードで定義
* `my`で宣言

```
my regex name { <[a..z]>+(xyz) }
say 'abcxyz' ~~ &name;    # abcxyz
```

---
## 名前付き正規表現

* `regex`の他に`token`と`rule`がある
* `token`
    * バックトラックしない
* `rule` 
    * バックトラックしない
    * 空白をそのままマッチさせる

---

## 名前付き正規表現
```
my token name { (<[a..z]>+)(xyz) }
say 'abcxyz' ~~ &name;    # Nil

my rule name { (<[a..z]>)+ '=' (xyz) }
say 'abc=xyz' ~~ &name;    # abc=xyz 
say 'abc = xyz' ~~ &name;  # abc = xyz 
```

---
## Grammar

* `grammar`で定義
* 名前付き正規表現をグルーピングできる
* 構文解析を簡単に実行できる

---
## Grammarの例

```
grammar TestGrammar {
    token TOP { ^ <number> $ }
    token number { \d+ }
}

class TestActions {
    method TOP($/) {
      $/.make(~$/);
    }
}

my $actions  =  TestActions.new;
my $match  =  TestGrammar.parse('40', :$actions);
say $match;         # ｢40｣
say $match.made;    # 40
```
---
## リファレンス

* `item`コンテキストで配列やハッシュを評価するとリファレンスが生成される
* `item()`を呼び出せば、`item`コンテキストで評価されてリファレンスが生成される
* 配列やハッシュであればスカラー変数へ代入するだけでリファレンスが生成される
* リファレンス要素の参照や代入は、`[]`や`<>`を使う
* Perl5のように`.[]`や`.{}`を使う事も可能
* サブルーチンのリファレンスは、`&subname`で生成される

---
## リファレンス

```
# 配列リファレンス
my $a = [1, 2, 3];

# .[]で要素を参照
# .なしでも可能
say $a.[0];      # 1
say $a[0];       # 1

# .[]で要素を代入
$a.[0] = 2;
$a[0] = 2;

# ハッシュリファレンス
my $b = {a => 1, b => 2};

# キーをベアワードで指定する場合は<>を使う
say $b.<a>;      # 1
say $b<a>;       # 1

# {}を使う場合はキーを""で囲む
say $b.{"a"};    # 1
say $b{"a"};     # 1

# .{} .<>で要素を代入
$b.{"a"} = 2;
$b{"a"} = 2;

$b.<a> = 3;
$b<a> = 3;

my @a = (1, 2, 3, 4);

# スカラー変数に配列を代入するとリファレンスになる
# itemコンテキストでの代入はリファレンスを返す
my $aref = @a; 
say $aref.[1];   # 2
say $aref.[3];   # 4

# 同じ意味(itemコンテキストで評価)
$aref = item(@a);
say $aref.[1];   # 2
say $aref.[3];   # 4

my %b = (a  => 1, b => 2, c => 3); 

# スカラー変数にハッシュを代入するとリファレンスになる
# itemコンテキストでの代入はリファレンスを返す
my $bref = %b;
say $bref.{"a"}; # 1
say $bref.<a>;   # 1
say $bref.{"c"}; # 3
say $bref.<c>;   # 3

# 同じ意味(itemコンテキストで評価)
$bref = item(%b);
say $bref.{"a"}; # 1
say $bref.<a>;   # 1
say $bref.{"c"}; # 3
say $bref.<c>;   # 3

# サブルーチンを定義
sub hello { say 'Hello' }

# &でサブルーチンのリファレンス生成
my $subref = &hello; 

# リファレンスでサブルーチン呼び出し
$subref.();    # Hello
$subref();     # Hello
```

---
## デリファレンス

* `()`を使う
* `@()`、`%()`、`&()`という形で使う

```
my $a = [1, 2, 3];

# 配列のデリファレンス
my @a = @($a);
say @a[1];     # 2
say @a[2];     # 3

# ハッシュのデリファレンス
my $b = {a => 1, b => 2};
my %b = %($b);
say %b.<a>;   # 1
say %b.<b>;   # 2

# サブルーチンのデリファレンス
sub hello { say 'Hello' }
my $sub_ref = &hello;
&($sub_ref)();  # Hello
```

---
## モジュール

* `module`キーワードでモジュールを定義
* `is export`で`export`するサブルーチンやメソッドを指定 
* `unit module A;`でファイルの残り全体をモジュールのスコープにする
* `class`はデフォルトで`export`される

---
## モジュール
```
unit module A::B;  # unitでファイルの最後までA::Bモジュール

sub foo is export { ... }

class A::B::C { ... }   # クラスはデフォルトでexportされる
```


```
use lib 'lib';
use A::B;

foo();
A::B::C.new;
```

---
## モジュールのインストール

* pandaを使う
* 依存モジュールなどまとめてインストール

```
$ git clone --recursive git://github.com/tadzik/panda.git
$ cd panda
$ perl6 bootstrap.pl
```

---
## Perl6のドキュメント

* http://perl6.org/ 
    * 公式サイト 
* http://doc.perl6.org/
    * 公式のドキュメント
    * チュートリアルなどが充実している
    * とりあえず最初にここを読む 
* http://design.perl6.org/ 
    * デザインドキュメント
    * 主にLarryが書いてる
