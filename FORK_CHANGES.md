# Fork changes

This repository is a fork of [Traverse-Research/gpu-allocator](https://github.com/Traverse-Research/gpu-allocator), used as a vendored submodule. Changes relative to upstream `0.28.0`:

## Depend on the vendored ash

- `Cargo.toml`: `ash` (both the optional main dependency and the dev-dependency) now uses
  `path = "../ash/ash"` — the vendored [ash](../ash) fork (`0.38.0+1.4.352`) — instead of
  `ash 0.38` from crates.io. Feature sets are unchanged (`debug`, plus `debug, loaded` in
  dev-dependencies).

## Port pNext-chain calls to the vendored ash API

The vendored ash fork (post-`0.38.0`) removed the `push_next()` method from Vulkan structs
(it was renamed to the `unsafe extend()`) and moved `push()`/`extend()` onto the
`TaggedStructure` trait.

- `src/vulkan/mod.rs`: added `use ash::vk::TaggedStructure as _;` and replaced the three
  `MemoryAllocateInfo::push_next(&mut ...)` calls with the safe single-struct `push()`.
  The pushed structs are freshly `default()`-ed (or only set via field setters), so they
  never carry a `pNext` chain and `push()` cannot panic.
