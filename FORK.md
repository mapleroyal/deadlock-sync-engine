# Deadlock Sync engine fork

This is a small, independently maintained MIT-licensed fork of
[Deadlock API Ingest v0.4.8](https://github.com/deadlock-api/deadlock-api-ingest/tree/v0.4.8),
upstream commit `ef1882306163826809cb2768a39b4c4f8b258545`.
It supports the [Deadlock Sync supervisor](https://github.com/mapleroyal/deadlock-sync).
It is not an official upstream release.

Version `0.4.8-sync.1` adds `--own-matches --account-id ID32`, where ID32 is a
nonzero 32-bit Steam account ID. The engine selects only that remembered public
Steam account before decrypting its session. An absent or unusable selected
account fails with the existing authentication-error marker; another account is
never substituted. Without `--account-id`, upstream all-account behavior remains.
The supervisor owns current-account priority, per-account retries and cooldowns.

This patch does not change metadata request spacing, upstream attempt records,
quota rules, history order, authentication protections, memory permissions or
live reading. Steam sessions remain local and are used only to authenticate with
Steam. Do not run parallel workers or clear stored attempts to increase throughput.

Build and test locally only. This repository has no CI/CD or build workflow.
Tests use synthetic inputs and do not query real Steam accounts. GitHub stores
source and explicitly uploaded releases; it is not used to build them. The
supervisor's maintainer checksum-pins the locally built assets after validation.
Do not use upstream's GUI or self-installer alongside Deadlock Sync.

## Local builds

Use Rust 1.98.0, the checked-in Cargo.lock, and protoc. Keep development tools in
a private development workspace; a gaming Windows machine needs no SDK. Native
Linux builds use the host C compiler. Windows x64 can be cross-built on Linux
with the Rust `x86_64-pc-windows-gnu` standard library and a MinGW-w64 GCC/binutils
compiler (including windres), with those tools on the build process's PATH.
Set `CARGO_TARGET_X86_64_PC_WINDOWS_GNU_LINKER` and
`CC_x86_64_pc_windows_gnu` to that GCC executable if needed. Portable toolchains
can use workspace-local `RUSTUP_HOME`, `CARGO_HOME` and `CARGO_TARGET_DIR` without
changing global packages or shell profiles.

```sh
cargo +1.98.0 fmt --all -- --check
cargo +1.98.0 clippy --locked --all-targets -- -D warnings
cargo +1.98.0 test --locked --release
cargo +1.98.0 build --locked --release --target x86_64-unknown-linux-gnu
cargo +1.98.0 build --locked --release --target x86_64-pc-windows-gnu
```

Run native Windows checks on an available test environment separately; a Linux
cross-build alone does not prove Windows execution. Never wake a user's gaming
VM or displays without authorization. Build binaries stay outside source control.
