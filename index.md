---
marp: true
---

# 参照透過性と副作用と冪等性 

## ～安全な関数・メソッド・APIを作るために～

---

# 次の2つの関数の違いは？

```python=
def increment1(n: int) -> int:
    return n + 1

a = 1

def increment2() -> int:
    a = a + 1
    return a
```

* increment1は**参照透過**である関数で、increment2は参照透過でない関数

---

## 参照透過性

* 参照透過性とは
    * 「関数や式が、同じ入力に対して同じ結果を返す」性質のこと
* increment1は、参照透過な関数
    * 同じ整数値を与えれば同じ結果が返って来る
* increment2は、参照透過でない関数
    * 外部変数aの状態によって戻り値が変わりうる
    * 例えばa=1の状態で二回increment2を呼べばそれぞれ2,3と異なる値が返る

---

## 次の二つの関数の違いは？


```python=
def double_elements1(a: list[int]) -> list[int]:
    return [i*2 for i in a]


def double_elements2(a: list[int]) -> list[int]:
    for i in range:
        a[i] = a[i] * 2
    return a
```

* double_elements1は副作用がなく、double_elements2は副作用がある

---

## 副作用


* 副作用とは
    * 関数やプログラムの実行が、その外部の状態を変更すること
    * (主作用は入力に対して戻り値を返すこと)
* double_elements1は入力であるリストの状態を変更しないので副作用のない関数
* double_elements1は入力であるリストの状態を変更してしまうので副作用のある関数
* 副作用のある関数は参照透過でない場合が多い
    * increment2は副作用があってかつ参照透過でない

---

# 関数・メソッドは出来るだけ参照透過で副作用のないものにしましょう

---

## 参照透過で副作用のない関数は何がいいの？

* 利用者が分かりやすい
* プログラムのバグが減らせる
    * 副作用のある関数は外部の状態を変更させるため、知らないうちにリソースが変わっているといったことが起こりうる
* テストがしやすい
    * 参照透過性を満たさないコードは同じ入力に対しても結果が異なる場合があるため、テストを書くことが難しい

---

## 副作用を完全になくすことは難しいが...

* プログラムにおいて副作用を完全になくすことは難しい
  * DBへのレコードのinsertなど、外部の状態を変更させる操作はどこかしらでは発生しうる
* 重要なのは**可能な限り副作用をなくし**、**副作用がある場合は最小限に限定させ、それが分かるような**作りにすること
    * グローバル変数は基本的に使わない
    * 例えば`a.sort()`は副作用のある関数だが、戻り値がないため、リストの中身を直接書き換える操作であることが分かりやすい
    * オブジェクト指向プログラミングにおいては**カプセル化**により副作用の発生範囲を限定させる
        * インスタンス変数にはメソッドを通してアクセスする
        * まずはオブジェクトをイミュータブル(コンストラクタでのみ生成・変数の更新は不可)にできないかを考える

---

## 関数型プログラミング

* 参照透過で副作用のない関数を主に利用するプログラミングのスタイルが**関数型プログラミング**
* PythonやJavaといったオブジェクト指向言語と言われている言語も関数型はサポートされている
    * 高階関数(map, reduce)やlambda式など
    * オブジェクト指向と関数型のスタイルは完全に相反するものではない(最近の主流の言語は大体マルチパラダイム)
* 参照透過性が担保され、副作用のない純粋関数で記述する言語を純粋関数型言語という
    * Haskellなど
* IaCとして使われているterraformは宣言的で関数型のスタイルを取り入れている

---

## 参照透過性と冪等性

* 参照透過性と似たような言葉として、冪等性がある
    * 参照透過性は「関数や式が、同じ入力に対して同じ結果を返す」ことを意味し、冪等性は「同じ操作を何度も繰り返しても、結果が変わらない」ことを意味する
    * 数学的なイメージ
        * 冪等である：f(x) = f(f(x))が成立
            * 例：絶対値を取るabs(), 昇順(降順)にソート行う関数sorted()など
        * 参照透過である：f === f が成立
            * f(x) = f(x)が成立し、かつ作用が同じ

---

## 参照透過性と冪等性

* 参照透過性は関数・メソッドが満たしてほしい性質で、冪等性はAPIが満たしてほしい性質
    * 冪等でないAPIは安全ではない
        * 例えば金融システムでいうと冪等性を満たさないAPIは二重送金の問題が発生しうる
    * 特に分散システムにおいては冪等性は非常に重要
* 意味は異なるがそれぞれの重要な点は「利用者が**状態**を気にする必要がない」ということ
    * 関数やメソッドにとっての**状態**は入力値や外部の変数・オブジェクト
    * APIにとっての**状態**はリソースの結果

---

## まとめ

* **参照透過性**、**副作用**、**冪等性**は安全な関数・メソッド・APIを設計する上で特に考えるべき性質
    * 大事なのは状態を極力意識しなくてもよい設計にすること
* 今回は浅く広く紹介しました
    * 興味を持った方はそれぞれのトピックを書籍やweb上で深堀りしてみてください
        * オブジェクト指向プログラミング、関数型プログラミング、APIの冪等性、イミュータブルデータモデリングetc
    * 上記のトピックのより深い話(設計手法など)はまた別の機会に話せればと思います
