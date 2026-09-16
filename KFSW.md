# K-FSW changes

Fork of `spaceinventor/libparam`. K-FSW pins a revision of the `kfsw` branch in
its `west.yml`, so the pin cannot move under a release.

No changes from upstream. The branch exists to hold the pin and to have
somewhere to carry a patch if one becomes necessary.

Rules for this fork:

- One commit per change, each with a reason in the message.
- Rebase onto upstream `master`; do not merge upstream into `kfsw`.
- Keep this file current. When it lists nothing, the fork is only a stable pin.
