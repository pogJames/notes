Develop: Code in Rust → compile to .wasm → package as OCI image.
Deploy: Transfer image → ocre run (Linux) or flash firmware (Zephyr).
BSP: Use existing if board supported; copy/tweak for custom — focus on device tree.
