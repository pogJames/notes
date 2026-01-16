## Crates and Cargos

**CRATE**: single `.rs` file
1. Binary Crate: can compile + can execute `main.rs`
2. Library Crate: define functions in libraries `lib.rs`
```bash
# Compile Crate
$ rustc `crate-name`
# Run Crate
$ ./`crate-name`
```

**CARGO**: build system and package manager
1. `src`: main crates
2. `target`: compiled artifacts
3. `Cargo.toml`: packages, libraries, dependencies
4. `Cargo.lock`: compiled dependencies
```bash
# Create cargo
$ cargo new `cargo-name`
$ cd `cargo-name`

# Build cargo
$ cargo build

# Execute main file
$ ./target/debug/`cargo-name`

# Build + Execute
$ cargo run

# Check cargo compiles
```

## Ownership

## Structs

## Collections

## Macros
