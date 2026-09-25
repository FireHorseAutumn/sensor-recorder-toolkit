# sensor-recorder-toolkit (B1–B3, +B4/B5 optional)

High-rate sensor recording I/O toolkit. Pure systems work, Linux-only,
header-only, no dependencies beyond liburing/FUSE3 for the optional pieces.

## Components
- **B1. mmap streaming/ring writer** — pre-allocated MAP_SHARED writer +
  two-window ring writer (bounded file for continuous capture) + zero-copy
  reader.
- **B2. io_uring async writer** — minimal correct liburing write path: owned
  buffers until CQE, SQ-full retry, drain+fdatasync.
- **B3. Pinned-pool + SoA memory toolkit** — PinnedPool (cudaHostAlloc slab,
  free-list, THP hint), CacheAlignedArray, variadic SoA.
- **B4. FUSE write-buffer FS** (optional) — passthrough FUSE3, 4 MB write
  coalescing, optional zstd.
- **B5. Kernel telemetry chardev** (low priority) — didactic chardev-with-mmap
  example.

## Honest numbers to carry (the credibility)
- mmap wins 6.5x for many-small-files and 25x on reads, but LOSES ~1.7x to
  ofstream on single sequential writes. Ring writer fixes the 1 GB prealloc
  ceiling.
- io_uring: negligible on local NVMe, significant on network FS/eMMC.
- Pinned alloc: value is DMA-overlap enablement, not the workload itself.
  Publish the <1% hot-path number.

## Layout
- `include/srt/` — headers (the library)
- `src/`         — non-header pieces (FUSE daemon, chardev module)
- `tests/`       — correctness (round-trip, truncation, crash-recovery)
- `benchmarks/`  — write/read throughput matrix; JSON out

## Standalone
No sibling repos, no archive code. Dependencies: libc (Linux), CUDA
toolkit for B3 only, liburing for B2 only, FUSE3 for B4 only — every piece
is optional-gated so the core (B1 mmap/ring writer) builds with a bare
compiler.

## TODO (in order)
1. [ ] `include/srt/mmap_writer.hpp` — pre-alloc MAP_SHARED writer
2. [ ] `include/srt/ring_writer.hpp` — two-window ring
3. [ ] `tests/test_round_trip.cpp` — write/read/crash-recover
4. [ ] `include/srt/io_uring_writer.hpp` — liburing path
5. [ ] `include/srt/pinned_pool.hpp`, `soa.hpp`
6. [ ] `benchmarks/bench_io.cpp` — ofstream vs mmap vs ring vs uring; JSON out
7. [ ] README: honest applicability table
