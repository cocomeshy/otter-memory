# memory

Cross-platform memory allocation and string helpers for Otter.

Uses `VirtualAlloc`/`VirtualFree` on Windows, `mmap`/`munmap` on Linux/macOS — no libc dependency.

## API reference

### `memory.alloc(size:int) -> rawptr`

Allocate `size` bytes of memory. Returns a raw pointer to the allocated block.

- **Parameters:** `size` — Number of bytes to allocate (minimum 1)
- **Returns:** `rawptr` to the allocated memory, or null on failure
- **Platform:** `VirtualAlloc` (Windows), `mmap` (Linux/macOS)

```otter
rock buf:rawptr = memory.alloc(1024);
defer memory.free(buf);
```

---

### `memory.free(ptr:rawptr) -> void`

Release memory previously allocated with `alloc`.

- **Parameters:** `ptr` — Pointer returned by a previous `alloc` call (or null)
- **Platform:** `VirtualFree` (Windows), `munmap` (Linux/macOS)

```otter
rock buf:rawptr = memory.alloc(64);
memory.free(buf);
```

---

### `memory.memcpy(dst:rawptr, src:rawptr, n:int) -> rawptr`

Copy `n` bytes from `src` to `dst`. Regions must not overlap.

- **Returns:** `dst`
- **Platform:** `RtlMoveMemory` (Windows), byte/word loop (Unix)

```otter
rock src:rawptr = memory.alloc(16);
rock dst:rawptr = memory.alloc(16);
memory.memcpy(dst, src, 16);
```

---

### `memory.strlen(text:str) -> int`

Byte length of a null-terminated string (excluding the terminator).

```otter
rock len:int = memory.strlen("hello");  // 5
```

---

### `memory.str_from_int(value:int) -> str`

Allocate a new null-terminated string with the decimal representation of `value` (handles zero and negatives).

```otter
rock s:str = memory.str_from_int(-42);
```

---

### `memory.str_concat(a:str, b:str) -> str`

Allocate a new string containing `a` followed by `b`, null-terminated. The result must be freed when you no longer need it (same as other `alloc`-backed strings).

```otter
rock s:str = memory.str_concat("hello", "world");
```

The compiler also lowers `ptr + ptr` (including f-strings like `$"x {n}"`) to this function when `use memory` is in scope and `pkg/memory` includes `str_concat`.

## Language integration

- **Cast `(str)some_int`** — The compiler may emit its own small helper for literals; for full control, use `memory.str_from_int`.
- **String `+` and f-strings** — Require `memory.str_concat` in the linked program (add `use memory`, ensure `pkg/memory` is complete, run `otter pkg pull`).

## Dependencies

None — this is the lowest-level standard package.

## Install

```bash
otter pkg add memory
otter pkg pull
```
