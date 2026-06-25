---
title: "Three Lines That Shrunk My Rust Lambda by 8MB"
description: "Three Cargo.toml settings that reduced my Rust Lambda binary by 8MB and cut cold start latency."
publishDate: "2026-06-19"
tags: ["rust", "llvm", "computer-science", "technology"]
pinned: true
---

The default release profile in Rust isn't optimized for production Lambda deployments. Rust's standard release configuration prioritizes build speed over output quality, which directly impacts cold start latency in serverless environments.

## The Solution

Three configuration lines added to `Cargo.toml` dramatically reduced binary size:

```toml
[profile.release]
codegen-units = 1
lto = "fat"
panic = "abort"
```

## How Each Setting Works

**codegen-units = 1** compiles the entire crate as a single unit rather than parallel chunks. This gives the optimizer complete visibility across the codebase, enabling better inlining and dead code elimination.

**lto = "fat"** extends optimization across all dependencies through full Link Time Optimization. This allows LLVM to inline code across crate boundaries and deduplicate monomorphized generics that would otherwise remain isolated.

**panic = "abort"** removes unwinding machinery entirely, since a panicking Lambda will restart anyway. This eliminates unnecessary generated code from the binary.

## Results

These three settings combined produced approximately 8MB of binary size reduction, translating to measurable cold start improvements. The tradeoff is significantly slower release builds — these settings should only appear in `[profile.release]`, never in development profiles.