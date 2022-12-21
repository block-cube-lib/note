# 第Ⅱ部 ループとロジックの単純化
## 7章 制御フローを読みやすくする
### 条件式の並び順
`if (length <= 10)`のほうが`if (10 >= length)`　よりも  
`while (bytes_received < bytes_expected)`のほうが`while (bytes_expected > bytes_received)`よりも  
読みやすい。

左側は「捜査対象」の式。変化するもの  
右側は「比較対象」の式で変化しない。

個人的には数直線上の並びになることを意識していたけれど読みやすさで考えたほうがいい場面は多そう。

### if/else ブロックの並び順
- 条件は否定形よりも肯定形を使う
- 単純な条件を先に書く
- 関心を引く条件や目立つ条件を先に書く

ただしこの優劣は衝突することもある。  
否定形であってもやん順で関心や注意を引く場合もある。その場合は先に処理する法が良い。

### 三項演算子
良いときも悪いときもある。  
コードの長さではなく読みやすさを優先する。

### do/whileループを避ける
ループの中身をみてから条件を見ることになるのでコード読む手間が増えるし理解しづらい。  
あとこんな場合はどうなるかとか分かりづらい。
```c
do {
    continue;
} while (false);
```
ビャーネ・ストロヴストルップもdo-statementを避けるらしい。

### 関数から早く返す
早期returnの話。
> 関数で複数の return 文を使ってはいけないと思っている人がいる。アホくさ。

わかる  
そもそも関数の出口を一つにしたいのはクリーンアップの処理を確実に実行したいから。Cではスコープを抜けたときの処理とか書けなかったけどC++ならデストラクタ、C#ならusingがあるので問題ない。

### 悪名高きgoto
上から下へかつ飛び先が1つならOK。複数の飛び先とか下から上とかの`goto`はやめておけ。  
そもそもC言語以外は必要ない。代わりになるもっとわかりやすい機能がある。

### ネストを浅くする
早期`return`や`continue`を使用してネストを練らす

### 実行の流れを終えるかい？
コードの流れがかんたんに負えなくなる要素
- スレッド
- シグナル/割り込みハンドラ
- 例外
- 関数ポインタと無名関数
- 仮想メソッド

### まとめ
- 比較を書くときは変化する値を左に
- if/else文のブロックは適切に並び替える
  - 一般的には、肯定形・単純・目立つものを先に処理
- 三項演算子, do/while, gotoなどで読みにくくなることが多い
  - 代替がある
- ネストしたコードを追うのに集中力がいる
  - 早めに返す(returnする)
  - 「ガード節」(関数の上部で単純な条件を先に処理する)を使用する

## 8章 巨大な式を分割する
人間は一度に3~4の「もの」しか考えられない。コードの式が大きくなれば理解が難しくなる。

### 説明変数
```
if line.split(':')[0].strip() == "root":
```

```
username = line.split(':')[0].strip()
if username == "root":
```

### 要約変数
```cpp
if (request.user.id == document.owner_id) {
  // 編集可能
}

if (request.user.id != document.owner_id) {
  // 読み取り専用
}
```

```cpp
bool user_owns_document = request.user.id == document.owner_id;
if (user_owns_document) {
  // 編集可能
}

if (!user_owns_document) {
  // 読み取り専用
}
```

### ド・モルガンの法則を使う
```cpp
if (!(file_exists && !is_protected)) { Error("could not readi file"); }
```

```cpp
if (!file_exists || is_protected) { Error("could not readi file"); }
```

### 短絡評価の悪用
```cpp
if (a || b)
```
aがtrueならbは評価されない。これを悪用すると複雑なロジックになる

```cpp
assert((!(bucket = FindBucket(key))) || !bucket->IsOccupied());
```

```cpp
bucket = FindBucket(key);
if (bucket != NULL) assert(!bucket->IsOccupied());
```
