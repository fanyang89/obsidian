---
title: Rust app getting started
---

# Rust app getting started

#clap #Rust

```bash
cargo add anyhow -F backtrace
cargo add tokio -F full
cargo add thiserror
cargo add tracing
cargo add tracing-subscriber -F chrono
```

```rust
use clap::{Parser, Subcommand};

#[derive(Parser)]
#[command(about, long_about = None, propagate_version = true, arg_required_else_help = true)]
struct Cli {
    #[command(subcommand)]
    command: Option<Commands>,
}

#[derive(Subcommand)]
enum Commands {
    Add { name: Option<String> },
}

fn main() {
    let cli = Cli::parse();

    match &cli.command {
        Some(Commands::Add { name }) => {
            println!("'myapp add' was used, name is: {:?}", name)
        }
        None => {
            println!("Default subcommand");
        }
    }
}
```
