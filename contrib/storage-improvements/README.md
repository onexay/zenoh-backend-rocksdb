# RocksDB local synchronization

This branch implements the storage trait's explicit local synchronization operation. It accompanies the adjacent `zenoh` checkout's storage-readiness and acknowledged-write changes.

`supports_local_sync()` is true for writable RocksDB instances. `sync()` calls `flush_wal(true)` after the batch containing payload and recovery metadata. Read-only instances do not advertise synchronization support. Put results distinguish new keys from replacements.

Use the wrapper to build on Linux or in MSL from macOS with the pinned Zenoh fork:

```sh
./contrib/storage-improvements/cargo build --features zenoh/runtime_plugins
./contrib/storage-improvements/cargo test --features zenoh/runtime_plugins --lib
./contrib/storage-improvements/cargo clippy --features zenoh/runtime_plugins --lib --tests -- -D warnings
```

The manifest pins Zenoh fork commit `167c5dd18ae56a006abc8476d0baa84cd0790606`. The wrapper uses these pinned Git dependencies by default. Set `ZENOH_LOCAL_SOURCE=1` to apply Cargo path patches to the adjacent Zenoh checkout for development across both repositories. This override can change Cargo.lock; restore the committed lockfile before building the pinned combination.

Run the combined live proof from the adjacent Zenoh checkout:

```sh
../zenoh/contrib/storage-improvements/verify
```

See the [combined contract and runbook](https://github.com/onexay/zenoh/blob/167c5dd18ae56a006abc8476d0baa84cd0790606/contrib/storage-improvements/README.md). A synchronized local WAL does not establish replica quorum, exactly-once execution, or hardware behavior under power loss.
