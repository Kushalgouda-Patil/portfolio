---
title: "One Structural Change That Cuts Rust Lambda Cost"
description: "You're not losing time in your business logic, you're losing it before it even starts."
publishDate: "2026-03-10"
tags: ["aws", "rust", "cloud", "technology"]
---

*You're not losing time in your business logic, you're losing it before it even starts.*

If your Rust Lambda function is underperforming on warm invocations, the culprit probably isn't your logic. It's where you're setting up your AWS client. This one structural change will fix it.

### **How Lambda Execution Works**

- **Cold start** — A new container is spun up. The `main` function runs. Everything initializes from scratch.
- **Warm invocation** — The container is reused. `main` does NOT run again. Only handler function is called.

### **The Problem: Initializing the Client Inside the Handler**

```rust
async fn handler(event: LambdaEvent<SqsEvent>) -> Result<(), Error> {
    let config = aws_config::load_from_env().await;
    let client = Client::new(&config);
    // ❌ 👆 runs every time

    // your actual logic here
    Ok(())
}
#[tokio::main]
async fn main() -> Result<(), Error> {
    run(service_fn(handler)).await
}
```

What's happening on every single warm invocation:

- AWS config is loaded from environment variables again
- Credentials are resolved again
- Region is parsed again
- The HTTP client inside the SDK is reconstructed again
- **None of this has changed since the last call**

You're paying a setup cost that serves no purpose on warm containers.

### **The Fix: Move Client Init Into `main`**

```rust
async fn handler(
    state: &AppState,
    event: LambdaEvent<SqsEvent>,
) -> Result<(), Error> {
    let _client = &state.client;
    // ✅ 👆 already built, just borrow it

    // your actual logic here
    Ok(())
}
#[tokio::main]
async fn main() -> Result<(), Error> {
    let config = aws_config::load_from_env().await; // ✅ runs ONCE
    let client = Client::new(&config);              // ✅ 👆 runs ONCE

    let state = AppState { client };
    run(service_fn(|event| handler(&state, event))).await
}
```

### **Why This Works**

- `main` runs **once** when the Lambda container is first created
- Every warm invocation **skips** `main` entirely and jumps straight to your handler
- By building your client in `main`, it lives in memory and is simply **borrowed** on each call
- Rust's ownership model makes this safe — the `&AppState` reference is immutable and shared cleanly across invocations

### **When This Matters Most**

- Lambda is behind **API Gateway** handling frequent requests
- Running an **SQS consumer** that queries **DynamoDB** processing high message volume
- Have **scheduled or event-driven functions** that fire repeatedly
- Optimizing for **p99 latency** in production workloads

### **Latency Reduction Per Invocation**

| Scenario | Before | After | Realistic Reduction |
|---|---|---|---|
| Simple DynamoDB `get_item` | ~15–30ms | ~8–15ms | **~20–35% faster** |
| Write + read operation | ~25–45ms | ~12–20ms | **~25–40% faster** |
| Multiple AWS clients (S3 + DynamoDB + SQS) | ~35–60ms | ~12–20ms | **~30–45% faster** |

### **Summary**

| Scenario | Inside Handler | Inside Main |
|---|---|---|
| Config loading | Every invocation | Once per container |
| Client construction | Every invocation | Once per container |
| Warm invocation overhead | High | Near zero |

### **Key Takeaway**

> Move your AWS config and client initialization into `main`, before you register the handler. Warm invocations skip `main` entirely so anything you build there is free on every subsequent call.