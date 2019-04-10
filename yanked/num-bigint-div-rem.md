This is a breaking change.

# Summary
[summary]: #summary

Change the output type of all `Rem` implementations to the smaller type. Change the output type of all `Div` implementations to the dividend (this means `Div` works the same way as `DivAssign`. This is sound as `|a| >= |a % b| < |b|` and `|a / b| <= |a|` is always true for integer values.
Stop implementing operations with unsigned integers for `BigInt`, as `-1 / 1u8` does not fit into a unsigned integer and a different implementation would be surprising. 

# Motivation
[motivation]: #motivation

- **performance**: By returning a native integer we remove the need to allocate on the heap, which leads to a 2x speedup with small `BigInt`s. More exact benchmarks will follow in case it's deemed necessary. This also reduces the amount of `BigInt`s in general, as many people probably keep an integer as a `BigInt` for longer than necessary, which also has a negative impact on performance.

- **type safety**: Updating `Div` and `Rem` to return the smallest type possible, prevents accidental panics, as the problems of the following kind get now prevented at compile time:

```rust
fn calculate_divisor() -> u32 { 7 } 
let bigint: BigUint = BigInt::from(std::u32::MAX as u64 + 1);
let divisor = calculate_divisor();


let bigint_result = bigint % divisor;
let scalar_result = (bigint % divisor).to_u32().unwrap(); // this currently never panics
// with this RFC
let bigint_result = BigInt::from(bigint % divisor);
let scalar_result = bigint % divisor;
```
in case `calculate_divisor` gets changed to return a bigger integer type instead, i.e
```rust
fn calculate_divisor() -> u64 {
    std::u64::MAX
}
```
the old version now panics, while the new one causes a compile time error after this RFC is implemented. As `calculate_divisor` can be arbitrarily far away from the actual panic source, bugs of this kind can often be very unexpected.
 

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

Unlike other numeric operations, some `Div` and `Rem` implementation do not return a `BigInt`, in case the result is required as a `BigInt`, replace `bigint % small_value` with `BigInt::from(bigint % small_value)` .

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

`uX`  means all of  `u8, u16, u32, u64, u128, usize`.
`iX` means all of  `i8, i16. i32, i64, i128, isize`.
`Binop` means all of `Add, Rem, Div, Mul, Sub`

- Remove all implementations of
`Binop<BigInt> for uX`
`Binop<uX> for BigInt`
`BinopAssign<uX> for &BigInt`

- Change `type Output` of the following implementations
`Div<BigUint> for uX` -> `uX`
`Rem<uX> for <BigUint>` -> `uX`
`Rem<BigUint> for <uX>` -> `uX 
`Div<BigInt> for iX` -> `iX`
`Rem<iX> for <BigInt>` -> `iX`
`Rem<BigInt> for <iX>` -> `iX 

# Drawbacks
[drawbacks]: #drawbacks

- it can be surprising that some operations of big integers do not return big integers. As the compiler errors are pretty good in this case, this is not too big of a problem.

- we remove support for operations with unsigned integers for `BigInt`s. While I still think that this change is worth it, this has a negative impact.

- `std::iX::MIN / BigInt::from(-1)` will now panic, this should rarely happen.

# Rationale and alternatives
[rationale-and-alternatives]: #rationale-and-alternatives

- do not do anything. As the change improves both safety and performance, this seems undesirable.
- implement these methods in new trait, i.e.
```rust
pub trait ReducingDiv<RHS> {
    type Output;
    fn reducing_div(self, other: RHS) -> Self::Output;
}

pub trait ReducingRem<RHS> {
    type Output;
    fn reducing_rem(self, other: RHS) -> Self::Output;
}
```
this causes the list of trait implementations for `BigInt` and `BigUint` to be even more cluttered and requires an additional import to use.

- keep the current implementations of `Binop<uX> for BigInt` etc and implement the rest of this RFC, this seems really confusing and inconsistent, therefore I am strongly against it.