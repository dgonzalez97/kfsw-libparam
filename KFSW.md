# K-FSW changes

Fork of `spaceinventor/libparam`. K-FSW pins a revision of the `kfsw` branch in
its `west.yml`, so the pin cannot move under a release.

## Changes

### `param_t` alignment on 32-bit targets

`include/param/param.h`: `_Alignas(8)` on the leading `uint64_t vaddr`.

`src/param/list/param_list.c` computes the record stride as
`ALIGN_UP(sizeof(param_t), 8)` and asserts `__alignof__(param_t) == 8`. A
`uint64_t` is 8-aligned on x86-64 but 4-aligned under `-m32`, so the assertion
failed before anything compiled and no 32-bit target could build with PARAM in
the composition.

The record stride is unchanged on both widths, measured: 48 bytes on 32-bit and
80 on 64-bit, before and after, because `ALIGN_UP` already rounded to the same
value. Member offsets are unchanged. Stored layouts and anything reading the
stride are therefore unaffected.

### `param_heap` alignment on 32-bit targets

`src/param/list/param_list.c`: the static `param_heap` array is `aligned(8)`
instead of `aligned(4)`.

`param_heap_t` embeds `param_t`, now 8-aligned, and a union holding a
`uint64_t`, so the structure's own alignment is 8. `aligned(4)` asked for less
than that, which GCC grants, and under `-m32` the array was placed at a
4-mod-8 address: UBSan reported `type_mismatch_v1 ... member_access_within` at
the first member write in `param_list_alloc()` and the suite aborted. On
x86-64 the array happened to land 8-aligned anyway, so only 32-bit saw it.

Raising the request cannot move a member or change a stored layout; it only
stops the compiler from under-aligning the array. `param_buffer` keeps
`aligned(4)`: its offsets are rounded to 4 by `param_list_alloc()`, so an
8-byte value can still start at a 4-mod-8 offset there. Nothing has reported
it, and fixing it means changing the rounding, not the attribute.

Rules for this fork:

- One commit per change, each with a reason in the message.
- Rebase onto upstream `master`; do not merge upstream into `kfsw`.
- Keep this file current. When it lists nothing, the fork is only a stable pin.
