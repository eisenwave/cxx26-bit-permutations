[![CMake on multiple platforms](https://github.com/eisenwave/cxx26-bit-permutations/actions/workflows/cmake-multi-platform.yml/badge.svg)](https://github.com/eisenwave/cxx26-bit-permutations/actions/workflows/cmake-multi-platform.yml)

# C++26 Bit Permutations

This project implements
[P3104: Bit Permutations](https://eisenwave.github.io/cpp-proposals/bit-permutations.html).
Ironically, while this project has "C++26" in its name,
the proposal didn't make it into C++26,
but will almost certainly be in C++29.
See <https://github.com/cplusplus/papers/issues/1768> for its status.

There is a single-header reference implementation of the functions

- `bit_reverse`
- `bit_repeat`
- `next_bit_permutation`
- `prev_bit_permutation`
- `bit_compressr` = `bit_compress`
- `bit_expandr` = `bit_expand`
- `bit_compressl`
- `bit_expandl`

There are also implementations of existing `<bit>` functions for the purpose of testing.
The standard library functions don't support `_BitInt` or 128-bit integers, so it was necessary
to circumvent them:

- `popcount`
- `countl_zero`
- `countl_one`
- `countr_zero`
- `countr_one`

There are some additional functions from other proposals:

- `clmul`
- `clmul_wide`
- `shl`
- `shr`

All functions are located in namespace `cxx26bp`.

This implementation aims to provide the fastest possible library implementation
for each of these functions,
using any possible hardware support.
This project is portable, and tries to support

- **Architectures:** x86, ARM
- **Compilers:** MSVC, GCC, Clang

## Functions

```cpp
template<class T>
  constexpr T bit_reverse(T x) noexcept;
```

*Constraints*:
`T` is an unsigned integer type.

*Returns*:
`x` with the order of bits reversed.
That is, the least significant bit of `x`
is the most significant bit of the result,
and vice versa.
Mathematically, given the width $N$ of `T`,
the $i^\text{th}$ bit $r_i$ of the result equals:

$$r_i = r_{N - i - 1}$$

---

On ARM, this function has the same effect as the `rbit` instruction.

```cpp
template<class T>
  constexpr T bit_repeat(T x, int l);
```

*Constraints*:
`T` is an unsigned integer type.

*Preconditions*:
`l` > 0.

*Returns*:
A bit pattern stored in `x` of length `l`,
repeated as many times as fits into the result.
Any extraneous bits are truncated.
Mathematically, the $i^\text{th}$ bit $r_i$ in the result equals:

$$r_i = x_{n \mod l}$$

```cpp
// also known as bit_compressr
template<class T>
  constexpr T bit_compress(T x, T m) noexcept;
```

*Constraints*:
`T` is an unsigned integer type.

*Returns*:
Each bit of `x` where the corresponding bit in `m` is `1`,
contiguously packed into the least significant bits of the result,
with their relative order preserved.
Any more significant bits of the result are `0`.

---

On x86, this function has the same effect as the `pext` instruction.

```cpp
template<class T>
  constexpr T bit_compressl(T x, T m) noexcept;
```

*Effects*:
Equivalent to `bit_reverse(bit_compressr(bit_reverse(x), bit_reverse(l)))`.

```cpp
// also known as bit_expandr
template<class T>
  constexpr T bit_expand(T x, T m) noexcept;
```

*Constraints*:
`T` is an unsigned integer type.

*Returns*:
The least significant bits of `x` at the bit positions where `m` has a `1` bit,
with their relative order preserved.
Any other bits of the result are `0`.

---

On x86, this functions has the same effect as the `pdep` instruction.

```cpp
template<class T>
  constexpr T bit_expandl(T x, T m) noexcept;
```

*Effects*:
Equivalent to `bit_reverse(bit_expandr(bit_reverse(x), bit_reverse(l)))`.

```cpp
template<class T, class S>
  constexpr T shl(T x, S s) noexcept;
```

*Constraints*:
Each of `T` and `S` is a signed or unsigned integer type.

*Returns*:
$x \times 2^s$ rounded towards negative infinity.

```cpp
template<class T, class S>
  constexpr T shr(T x, S s) noexcept;
```

*Constraints*:
Each of `T` and `S` is a signed or unsigned integer type.

*Returns*:
$x \times 2^{-s}$ rounded towards negative infinity.

```cpp
template<class T>
struct wide_result {
    T low_bits;
    T high_bits;
    // ...
};

template<class T>
  constexpr T clmul_wide(T x, T y) noexcept;
```

*Constraints*:
`T` is an unsigned integer type.

*Returns*:
The carry-less product (aka XOR product) of `x` and `y`.
The low bits of this product are stored in `low_bits`
of the `wide_result` object,
and the high bits are stored in `high_bits`.

```cpp
template<class T>
  constexpr T clmul(T x, T y) noexcept;
```

*Effects*:
Equivalent to `clmul_wide(x, y).low_bits`.
