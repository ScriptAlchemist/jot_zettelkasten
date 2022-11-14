# Rust Before Main

## The Journey

---

* What happens when we run a Rust binary?
* How do we get to the main function?
* What even is a Rust executable?
* What tools can we use to inspect it?
* All Linux

```
ld a, B
jmp +50
add a, 5
```
Not necessarily wrong. Just too simple.

Before we get started.. Some tips

* Computers are not magic 
* All of this is understandable
* Everything you see here was made by humans
* It's all a mix of deliberate choice & random circumstance
* At any given point, it's not always easy to tell which
* Unfortunately, explanations are not always the best.

## Prologue: The Rust Compilation Model

---

```
fn main() {
  println!("Hi");
}
goes to +
rustc/LLVM
to produce ->
Object Files

We take those object files
add with the linker +
and creates the binary
```

Our toy program

```rust
fn main() {
  std::process::exit(code:0)
}
```
This is 3.8MB that is too big. So in our `cargo.toml`
We can add

```rust
[profile.release]
strip = true
opt-level = "z"
lto = true
panic = "abort"
```
This will turn it into 268KB (93% smaller) but we want it smaller

```rust
#![no_std]
#![no_main]

extern crate libc;

#[no_mangle]
pub extern "C" fn main() -> isize {
  0
}

#[panic_handler]
fn my_panic(_info: &core::panic::PanicInfo) -> ! {
  loop {}
}
```

With this last change it should be 14KB or 97% smaller than 268KB
This falls in line with C files sizes.

I didn't get the same thing on Mac. The executable seems to 

This lecture got weird because I'm on mac and not Linux







