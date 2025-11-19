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

All functions are located in namespace `cxx26bp`.

This implementation aims to provide the fastest possible library implementation
for each of these functions,
using any possible hardware support.
This project is portable, and tries to support

- **Architectures:** x86, ARM
- **Compilers:** MSVC, GCC, Clang
