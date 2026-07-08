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

Allocates a contiguous block of memory. On Windows, uses VirtualAlloc with MEM_COMMIT (0x1000) and PAGE_READWRITE (4). On Linux/macOS, uses mmap with PROT_READ|PROT_WRITE and MAP_PRIVATE|MAP_ANONYMOUS. Unix allocations carry a 16-byte header: [size][liveness-magic]. The magic (2004318071) lets free() detect double-frees; the returned pointer is offset past the header so free() can recover the mapping base + length.

Parameters:

- `size`: Number of usable bytes to allocate

Returns: Pointer to the usable region, or null on failure

### `memory.free(ptr:rawptr)`

Releases a previously allocated memory block. On Windows, calls VirtualFree with MEM_RELEASE (0x8000). On Linux/macOS, recovers the mmap base from the 16-byte header, verifies the liveness magic (panics on a double-free / corrupted block), clears it, then munmaps. Passing null is a safe no-op.

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
