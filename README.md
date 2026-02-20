# memory

Cross-platform memory allocation for Otter.

Uses `VirtualAlloc`/`VirtualFree` on Windows, `mmap`/`munmap` on Linux/macOS — no libc dependency.

## API Reference

### `memory.alloc(size:int) -> rawptr`

Allocate `size` bytes of memory. Returns a raw pointer to the allocated block.

- **Parameters:**
  - `size` — Number of bytes to allocate (minimum 1)
- **Returns:** `rawptr` to the allocated memory, or null on failure
- **Platform:** Uses `VirtualAlloc` (Windows), `mmap` (Linux/macOS)

```otter
rock buf:rawptr = memory.alloc(1024);
defer memory.free(buf);
```

---

### `memory.free(ptr:rawptr) -> void`

Release memory previously allocated with `alloc`.

- **Parameters:**
  - `ptr` — Pointer returned by a previous `alloc` call
- **Platform:** Uses `VirtualFree` (Windows), `munmap` (Linux/macOS)

```otter
rock buf:rawptr = memory.alloc(64);
// ... use buf ...
memory.free(buf);
```

---

### `memory.memcpy(dst:rawptr, src:rawptr, n:int) -> rawptr`

Copy `n` bytes from `src` to `dst`. Regions must not overlap.

- **Parameters:**
  - `dst` — Destination pointer
  - `src` — Source pointer
  - `n` — Number of bytes to copy
- **Returns:** `dst`
- **Platform:** Uses `RtlMoveMemory` (Windows), byte-by-byte loop (Linux/macOS)

```otter
rock src:rawptr = memory.alloc(16);
rock dst:rawptr = memory.alloc(16);
memory.memcpy(dst, src, 16);
```

---

### `memory.strlen(text:str) -> int`

Count the number of bytes in a null-terminated string (excluding the null terminator).

- **Parameters:**
  - `text` — Null-terminated string
- **Returns:** Length in bytes

```otter
rock len:int = memory.strlen("hello");  // 5
```

> **Note:** Integer-to-string conversion and string concatenation are built into the language.
> Use `(str)x` to cast and `"a" + "b"` to concatenate — no imports needed.

## Dependencies

None — this is the lowest-level package.

## Install

```
otter pkg add memory
otter pkg pull
```
