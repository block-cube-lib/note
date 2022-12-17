2022年のRustのconst関係の更新メモ

[リリースノート](https://github.com/rust-lang/rust/blob/master/RELEASES.md)  
Versionは1.58.0 (2022-01-13)から1.66.0 (2022-12-15)まで

とりあえずconstっぽいもの片っ端から  

---

## Language
### 1.58.0
#### [*const T pointers can now be dereferenced in const contexts.](https://github.com/rust-lang/rust/pull/89551/)
定数文脈で`*const T`を逆参照できるようになった。  
ただし`*mut T`のようなミュータブルな参照は逆参照できない。

### 1.59.0
#### [Stabilize default arguments for const parameters and remove the ordering restriction for type and const parameters](https://github.com/rust-lang/rust/pull/90207/)
- const genericsのデフォルト値を設定できるようなった
- genericsパラメーターの順番がライフタイム→型→定数と固定だったのをライフタイム→型or定数とできるようになった

### 1.61.0
#### [const fn signatures can now include generic trait bounds](https://github.com/rust-lang/rust/pull/93827/)
`const fn`の中で関数ポインタの作成、返却、キャストを可能にした。  
関数ポインタから整数型へ変換や関数ポインタの呼び出しは不可。

##### 作成と返却
```rust
const fn get_function() -> fn() {
    fn foo() {
        println!("Hello, World!");
    }
    
    foo
}
```

##### キャスト
```rust
const fn get_function() -> fn() {
    unsafe {
        std::mem::transmute(0x1234usize)
    }
}
```

ただし以下のような関数ポインタから整数への変換はできない
```rust
const fn fn_to_usize(f: fn()) -> usize {
    f as usize  //~ pointers cannot be cast to integers during const eval
}
```

以下のような関数ポインタの呼び出しもできない
```rust
const fn call_fn_ptr(f: fn()) {
    f() //~ function pointers are not allowed in const fn 
}
```

#### [const fn signatures can now use impl Trait in argument and return position](https://github.com/rust-lang/rust/pull/93827/)
`const fn`の中でtrait bounds(ジェネリクス境界)を使用できるようになり`dyn Trait`オブジェクトの受け渡しが可能になった。
```rust
const fn do_thing<T: Foo>(_x: &T) {
    // ...
}
```
ただしTraitの関数を`const fn`として定義できないためTraitの関数呼び出しはできない。

#### [Function pointers can now be created, cast, and passed around in a const fn](https://github.com/rust-lang/rust/pull/93827/)
`const fn`の引数と戻り値で`impl Trait`が使用可能になった。
```rust
const fn do_thing(x: impl Foo) -> impl Foo {
    x
}
```
ただし関連型や関連定数にアクセスする以上のことはできない。

### 1.62.0
#### [Fix constants not getting dropped if part of a diverging expression](https://github.com/rust-lang/rust/pull/94775/)
特定の条件でdropが呼ばれないことがあったが修正された。  
以下のコードは`cba`と出力されるが修正前は`ca`と出力されていた。
```rust
struct Print(&'static str);

impl Drop for Print {
    fn drop(&mut self) {
        println!("{}", self.0);
    }
}

const A: Print = Print("a");
const B: Print = Print("b");

fn main() {
    loop {
        std::mem::forget(({A}, B, Print("c"), break));
    }
}
```

#### [const functions may now specify extern "C" or extern "Rust"](https://github.com/rust-lang/rust/pull/95346/)

以下のように`const fn`に`extern`, `extern "C"`, `extern "Rust"`をつけた定義ができるようになった。
```rust
const extern fn foo1() {}
```
```rust
const extern "C" fn foo2() {}
```
```rust
const extern "Rust" fn foo3() {}
```
```rust
const unsafe extern fn bar1() {}
```
```rust
const unsafe extern "C" fn bar2() {}
```
```rust
const unsafe extern "Rust" fn bar3() {}
```

### 1.66.0
#### [Change constant evaluation errors from a deny-by-default lint to a hard error](https://github.com/rust-lang/rust/pull/102091/)
定数文脈の評価時のエラーをリントのdenyからコンパイルエラーに変更した。

## Stabilized APIs
以下のAPIが安定化した。
#### 1.65.0
- [`<*const T>::cast_mut`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.cast_mut)
- [`<*mut T>::cast_const`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.cast_const)

### These APIs are now usable in const contexts:
以下のAPIが定数文脈で使用可能になった。
#### 1.58.0
- [`Duration::new`](https://doc.rust-lang.org/stable/std/time/struct.Duration.html#method.new)
- [`Duration::checked_add`](https://doc.rust-lang.org/std/time/struct.Duration.html#method.checked_add)
- [`Duration::saturating_add`](https://doc.rust-lang.org/std/time/struct.Duration.html#method.saturating_add)
- [`Duration::checked_sub`](https://doc.rust-lang.org/std/time/struct.Duration.html#method.checked_sub)
- [`Duration::saturating_sub`](https://doc.rust-lang.org/std/time/struct.Duration.html#method.saturating_sub)
- [`Duration::checked_mul`](https://doc.rust-lang.org/std/time/struct.Duration.html#method.checked_mul)
- [`Duration::saturating_mul`](https://doc.rust-lang.org/std/time/struct.Duration.html#method.saturating_mul)
- [`Duration::checked_div`](https://doc.rust-lang.org/std/time/struct.Duration.html#method.checked_div)

#### 1.61.0
- [`<*const T>::offset` and `<*mut T>::offset`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.offset)
- [`<*const T>::wrapping_offset` and `<*mut T>::wrapping_offset`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.wrapping_offset)
- [`<*const T>::add` and `<*mut T>::add`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.add)
- [`<*const T>::sub` and `<*mut T>::sub`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.sub)
- [`<*const T>::wrapping_add` and `<*mut T>::wrapping_add`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.wrapping_add)
- [`<*const T>::wrapping_sub` and `<*mut T>::wrapping_sub`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.wrapping_sub)
- [`<[T]>::as_mut_ptr`](https://doc.rust-lang.org/stable/std/primitive.slice.html#method.as_mut_ptr)
- [`<[T]>::as_ptr_range`](https://doc.rust-lang.org/stable/std/primitive.slice.html#method.as_ptr_range)
- [`<[T]>::as_mut_ptr_range`](https://doc.rust-lang.org/stable/std/primitive.slice.html#method.as_mut_ptr_range)

#### 1.63.0
- [`array::from_ref`](https://doc.rust-lang.org/stable/std/array/fn.from_ref.html)
- [`slice::from_ref`](https://doc.rust-lang.org/stable/std/slice/fn.from_ref.html)
- [`intrinsics::copy`](https://doc.rust-lang.org/stable/std/intrinsics/fn.copy.html)
- [`intrinsics::copy_nonoverlapping`](https://doc.rust-lang.org/stable/std/intrinsics/fn.copy_nonoverlapping.html)
- [`<*const T>::copy_to`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_to)
- [`<*const T>::copy_to_nonoverlapping`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_to_nonoverlapping)
- [`<*mut T>::copy_to`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_to-1)
- [`<*mut T>::copy_to_nonoverlapping`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_to_nonoverlapping-1)
- [`<*mut T>::copy_from`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_from)
- [`<*mut T>::copy_from_nonoverlapping`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_from_nonoverlapping)
- [`str::from_utf8`](https://doc.rust-lang.org/stable/std/str/fn.from_utf8.html)
- [`Utf8Error::error_len`](https://doc.rust-lang.org/stable/std/str/struct.Utf8Error.html#method.error_len)
- [`Utf8Error::valid_up_to`](https://doc.rust-lang.org/stable/std/str/struct.Utf8Error.html#method.valid_up_to)
- [`Condvar::new`](https://doc.rust-lang.org/stable/std/sync/struct.Condvar.html#method.new)
- [`Mutex::new`](https://doc.rust-lang.org/stable/std/sync/struct.Mutex.html#method.new)
- [`RwLock::new`](https://doc.rust-lang.org/stable/std/sync/struct.RwLock.html#method.new)

#### 1.64.0
- [`slice::from_raw_parts`](https://doc.rust-lang.org/stable/core/slice/fn.from_raw_parts.html)

#### 1.65.0
- [`<*const T>::offset_from`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.offset_from)

### Const-stable:
以下のAPIが定数文脈でも使用可能になった。
#### 1.59.0
- [`mem::MaybeUninit::as_ptr`](https://doc.rust-lang.org/stable/std/mem/union.MaybeUninit.html#method.as_ptr)
- [`mem::MaybeUninit::assume_init`](https://doc.rust-lang.org/stable/std/mem/union.MaybeUninit.html#method.assume_init)
- [`mem::MaybeUninit::assume_init_ref`](https://doc.rust-lang.org/stable/std/mem/union.MaybeUninit.html#method.assume_init_ref)
- [`ffi::CStr::from_bytes_with_nul_unchecked`](https://doc.rust-lang.org/stable/std/ffi/struct.CStr.html#method.from_bytes_with_nul_unchecked)

## Compatibility Notes
### 1.64.0
#### [Errors at const-eval time are now in future incompatibility reports](https://github.com/rust-lang/rust/pull/97743/)
将来的に定数文脈の評価時のエラーをリントのdenyからコンパイルエラーに変更するのでその前に将来エラーになるということを表示する。  
補足: これは1.66.0の更新内容の[Change constant evaluation errors from a deny-by-default lint to a hard error](https://github.com/rust-lang/rust/pull/102091/)の前段階の更新です。