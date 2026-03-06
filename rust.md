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

## Profiling
```bash
### 1. Install perf                                                                                                                  
sudo apt install linux-tools-generic                                                                                             
sudo ln -sf $(ls /usr/lib/linux-tools/*/perf | head -1) /usr/local/bin/perf                                                        
                                                                                                                                 
### 2. Verify it works (ignore the kernel version warning)                                                                           
sudo perf stat ls                                                                                                                  

### 3. Build release binary
cargo build --release

### 4. Run perf stat against the app
sudo perf stat ./target/release/modbus-stream --device /dev/ttyUSB0
# Let it run 10–20 seconds, then Ctrl+C
```
