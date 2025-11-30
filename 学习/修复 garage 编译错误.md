#pr #Rust

```
error[E0635]: unknown feature `stdsimd`
  --> /Users/fanyang/.cargo/registry/src/rsproxy.cn-0dccff568467c15b/crc32c-0.6.4/src/lib.rs:23:30
   |
23 | #![cfg_attr(nightly, feature(stdsimd))]
   |                              ^^^^^^^

For more information about this error, try `rustc --explain E0635`.
error: could not compile `crc32c` (lib) due to 1 previous error
warning: build failed, waiting for other jobs to finish...
```

https://github.com/rust-lang/rust/issues/27731
