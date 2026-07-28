# tinyparts

Parts-pack distribution repo for [tinyStudio](https://github.com/Mister-Industries/tinyStudio).
The app's Parts Packs panel (Circuit view → Components rail → package icon)
polls `index.json` in this repo and installs any pack the user selects.

## Layout

```
index.json                       top-level: lists every pack
packs/
  tinystudio-core/
    pack.json                    this pack's manifest
    ATTRIBUTION.md                Fritzing CC-BY-SA attribution
    parts/                       one PartDef JSON per part
```

## Packs

- **tinystudio-core** — 1776 parts converted from the Fritzing
  [fritzing-parts](https://github.com/fritzing/fritzing-parts) core library
  (breadboard + schematic views), via tinyStudio's
  `scripts/fritzing-import.mjs` + `scripts/make-pack-index.mjs`.

See tinyStudio's `docs/tinyparts-pack-setup.md` for how packs are generated
and published.
