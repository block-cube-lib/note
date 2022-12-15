2022年のRustのconst関係の更新メモ

[リリースノート](https://github.com/rust-lang/rust/blob/master/RELEASES.md)  
Versionは1.58.0 (2022-01-13)から1.66.0 (2022-12-15)まで

とりあえずconstっぽいもの片っ端から  

---

## Language
### 1.58.0
- [*const T pointers can now be dereferenced in const contexts.](https://github.com/rust-lang/rust/pull/89551/)

### 1.59.0
- [Stabilize default arguments for const parameters and remove the ordering restriction for type and const parameters](https://github.com/rust-lang/rust/pull/90207/)

### 1.61.0
- [const fn signatures can now include generic trait bounds](https://github.com/rust-lang/rust/pull/93827/)
- [const fn signatures can now use impl Trait in argument and return position](https://github.com/rust-lang/rust/pull/93827/)
- [Function pointers can now be created, cast, and passed around in a const fn](https://github.com/rust-lang/rust/pull/93827/)

### 1.62.0
- [Fix constants not getting dropped if part of a diverging expression](https://github.com/rust-lang/rust/pull/94775/)
- [const functions may now specify extern "C" or extern "Rust"](https://github.com/rust-lang/rust/pull/95346/)

### 1.66.0
- [Change constant evaluation errors from a deny-by-default lint to a hard error](https://github.com/rust-lang/rust/pull/102091/)

## Stabilized APIs
#### 1.65.0
- <*const T>::cast_mut
- <*mut T>::cast_const

### These APIs are now usable in const contexts:
#### 1.58.0
- Duration::new
- Duration::checked_add
- Duration::saturating_add
- Duration::checked_sub
- Duration::saturating_sub
- Duration::checked_mul
- Duration::saturating_mul
- Duration::checked_div

#### 1.61.0
- <*const T>::offset and <*mut T>::offset
- <*const T>::wrapping_offset and <*mut T>::wrapping_offset
- <*const T>::add and <*mut T>::add
- <*const T>::sub and <*mut T>::sub
- <*const T>::wrapping_add and <*mut T>::wrapping_add
- <*const T>::wrapping_sub and <*mut T>::wrapping_sub
- <[T]>::as_mut_ptr
- <[T]>::as_ptr_range
- <[T]>::as_mut_ptr_range

#### 1.63.0
- <*const T>::copy_to
- <*const T>::copy_to_nonoverlapping

#### 1.64.0
- slice::from_raw_parts

#### 1.65.0
- <*const T>::offset_from

### Const-stable:
#### 1.59.0
- mem::MaybeUninit::as_ptr
- mem::MaybeUninit::assume_init
- mem::MaybeUninit::assume_init_ref
- ffi::CStr::from_bytes_with_nul_unchecked

## Compatibility Notes
- [Errors at const-eval time are now in future incompatibility reports](https://github.com/rust-lang/rust/pull/97743/)
