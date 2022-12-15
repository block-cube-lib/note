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
- [`<*const T>::cast_mut`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.cast_mut)
- [`<*mut T>::cast_const`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.cast_const)

### These APIs are now usable in const contexts:
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
[`<*const T>::copy_to`]: https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_to
[`<*const T>::copy_to_nonoverlapping`]: https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.copy_to_nonoverlapping

#### 1.64.0
- [`slice::from_raw_parts`](https://doc.rust-lang.org/stable/core/slice/fn.from_raw_parts.html)

#### 1.65.0
- [`<*const T>::offset_from`](https://doc.rust-lang.org/stable/std/primitive.pointer.html#method.offset_from)

### Const-stable:
#### 1.59.0
- [`mem::MaybeUninit::as_ptr`](https://doc.rust-lang.org/stable/std/mem/union.MaybeUninit.html#method.as_ptr)
- [`mem::MaybeUninit::assume_init`](https://doc.rust-lang.org/stable/std/mem/union.MaybeUninit.html#method.assume_init)
- [`mem::MaybeUninit::assume_init_ref`](https://doc.rust-lang.org/stable/std/mem/union.MaybeUninit.html#method.assume_init_ref)
- [`ffi::CStr::from_bytes_with_nul_unchecked`](https://doc.rust-lang.org/stable/std/ffi/struct.CStr.html#method.from_bytes_with_nul_unchecked)

## Compatibility Notes
- [Errors at const-eval time are now in future incompatibility reports](https://github.com/rust-lang/rust/pull/97743/)
