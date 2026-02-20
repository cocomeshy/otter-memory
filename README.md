# memory

Cross-platform memory allocation and string operations for Otter.

Uses `VirtualAlloc`/`VirtualFree` on Windows, `mmap`/`munmap` on Linux/macOS — no libc dependency.

## API Reference

### `memory.alloc(size:int) -> rawptr`

Allocate `size` bytes of memory. Returns a raw pointer to the allocated block.

- **Parameters:**
  - `size` — Number of bytes to allocate (minimum 1)
- **Returns:** `rawptr` to the allocated memory, or null on failure
- **Platform:** Uses `VirtualAlloc` (Windows), `mmap` (Linux), `mmap` (macOS)

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

---

### `memory.str_from_int(value:int) -> str`

Convert an integer to its string representation. Handles negative numbers.

- **Parameters:**
  - `value` — Integer to convert
- **Returns:** New string containing the decimal representation

```otter
rock s:str = memory.str_from_int(42);     // "42"
rock neg:str = memory.str_from_int(-7);   // "-7"
```

---

### `memory.str_concat(a:str, b:str) -> str`

Concatenate two strings into a new allocated string.

- **Parameters:**
  - `a` — First string
  - `b` — Second string
- **Returns:** New string `a + b`

```otter
rock full:str = memory.str_concat("hello ", "world");  // "hello world"
```

## Dependencies

None — this is the lowest-level package.

## Install

```
otter pkg add memory
otter pkg pull
```
