# otter-memory

Manual memory allocation and low-level string helpers.

Part of the Otter standard library. Otter is a compiled systems language with no garbage collector and no libc dependency (pthread for threading is the one exception); everything else goes through raw syscalls and DLL imports.

## Install

In your `otter.nest`:

```nest
deps {
  use "memory" want "1.0.0"
}
```

Then:

```sh
otter pkg pull
```

## API reference

### `memory.alloc(size:int) -> rawptr`

Allocates a contiguous block of memory (zero-filled). On Windows, uses VirtualAlloc with MEM_COMMIT (0x1000) and PAGE_READWRITE (4). On Linux/macOS, small sizes (<= 4096) come from size-class freelists over 64 KiB mmap arenas; larger sizes map straight through to mmap. See the heap comment above for the header format and locking rules.

Parameters:

- `size`: Number of usable bytes to allocate (minimum 1)

Returns: Pointer to the usable region, or null on failure

### `memory.free(ptr:rawptr)`

Releases a previously allocated memory block. On Windows, calls VirtualFree with MEM_RELEASE (0x8000). On Linux/macOS, verifies the liveness magic (a block that is already FREE panics as a double free; anything else panics as corruption), marks the block FREE, then either pushes it onto its size-class freelist or, for large blocks, munmaps it. Passing null is a safe no-op.

Parameters:

- `ptr`: Pointer obtained from alloc(), or null

### `memory.memcpy(dst:rawptr, src:rawptr, n:int) -> rawptr`

Copies n bytes from src to dst, returning dst. On Windows, delegates to kernel32 RtlMoveMemory. On Unix, performs a manual 8-byte-word copy loop followed by a partial-word copy for any remaining bytes using bitmask merging.

Parameters:

- `dst`: Destination pointer
- `src`: Source pointer
- `n`: Number of bytes to copy

Returns: The destination pointer

### `memory.strlen(text:str) -> int`

Returns the byte length of a null-terminated string, excluding the terminator. On Windows, delegates to kernel32 lstrlenA. On Unix, performs a manual byte-by-byte scan checking for a zero byte by masking each 8-byte word with 0xFF.

Parameters:

- `text`: The null-terminated string to measure

Returns: Number of bytes before the null terminator

---

## Dependencies

none, this is the lowest-level package.

## License

MIT.
