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

Rules for this fork:

- One commit per change, each with a reason in the message.
- Rebase onto upstream `master`; do not merge upstream into `kfsw`.
- Keep this file current. When it lists nothing, the fork is only a stable pin.
