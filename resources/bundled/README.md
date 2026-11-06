# Factory mappings

These files are the default mappings shipped with APC Reaper Console `0.3.0-beta.22`.

Each file is the mapping checked Active in the maintainer library. The shipped copy sets `profile_version` to `1` and `origin` to `bundled`. The personal template it was copied from is not changed.

ReaPack installs each file as its own package, into `Data/APCReaperConsole/bundled/`. A package is downloaded again only when that file changes. That install does not write `user/`. A file already in `APCReaperConsole/bundled/` is left in place and is preferred when both copies exist.
