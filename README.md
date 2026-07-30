# tinyparts

Parts-pack distribution repo for [tinyStudio](https://github.com/Mister-Industries/tinyStudio).
The app's Parts Packs panel (Circuit view → Components rail → package icon)
polls `index.json` here and installs whichever packs you pick. Each installed
pack becomes its own tab in the components rail, the way Fritzing's parts bins
work.

## Layout

```
index.json              lists every pack, grouped SparkFun / Vendors
packs/
  <pack-id>/
    pack.json           this pack's manifest (schema, id, name, icon, parts[])
    ATTRIBUTION.md      Fritzing CC-BY-SA attribution
    parts/              one PartDef JSON per part
```

`index.json` entries carry `group`, `icon` (a 2-letter monogram for the tab
rail), `count` (tiles the palette shows) and `files` (parts on disk, which is
larger — see *Package variants*) alongside the usual `id` / `name` / `version`
/ `url`, so the installer can render grouped, countable rows without fetching
every manifest.

## The Core packs are gone — they ship with the app

There used to be ten `core-*` packs here (Basic, Input, Output, ICs, Power,
Microcontroller, Connection, Communication, Tools, Misc): 642 files, 554 tiles.
Making someone install a pack to get a resistor was the wrong trade, so that
whole catalogue is now bundled — it lives in the app under
`src/renderer/src/assets/parts` and shows up as the **Core** bin with no
download and no network.

An install that already downloaded those packs discards its copies on first
launch and picks up the bundled ones; `bundledPackIds` in tinyStudio's
`assets/packMigration.json` is what drives that. Nothing is re-downloaded, and
nothing in a saved circuit stops resolving.

What's left here is what stays optional: SparkFun's sub-bins and the
per-vendor packs.

## Why so many packs

Before the split there was a single `tinystudio-core` pack holding all 1776
parts. That was a 124 MB download to get one resistor, and it landed in the
palette as one undifferentiated tab. The library is split along the same lines
Fritzing uses for its bins — install only the bins you actually work with.

Parts are classified by, in order: explicit membership in a Fritzing `.fzb`
bin file, the `sparkfun-<subbin>-*` filename convention, vendor prefixes, then
keyword rules over family/label. See tinyStudio's
`scripts/split-parts-packs.mjs`.

## Package variants

Fritzing ships one `.fzp` per *PCB package*, and the package is frequently the
only difference. SparkFun titles all sixteen of its inductors "Inductors", all
thirteen ceramic caps "Capacitor" and all eighteen electrolytics "Capacitor
Polarized" — they differ in footprint, which is a view tinyStudio doesn't have.
Left alone that's sixteen, thirteen and eighteen indistinguishable squares.

So parts are grouped by **pack + family + base title**, and each group gets one
tile. The rest are marked `variantOf` and listed under the primary's
`variants`. **The variant files stay in the pack** — a saved document that
references `smd-resistor-0603` must keep resolving — and the palette's search
still reaches them, carrying the package in their name ("Capacitor (0805)").
They just don't get their own square. That's why `files` exceeds `count`.

Two guards keep the fold honest, because Fritzing families sometimes lump
together parts that aren't interchangeable:

- **Pin names.** Connections in a saved circuit are keyed by pin name, so parts
  offering different pin names never fold. That's what keeps the 2-pin and
  3-pin ceramic resonators — same family, both titled "Resonator" — apart.
- **Behaviour tokens.** A package string carrying `anode`/`cathode`,
  `npn`/`pnp`, `spst`/`spdt`/`dpdt`, `male`/`female`, an EBC/ECB/CBE pinout or
  a waveform name splits its group. The nine 7-segment displays stay as two
  tiles, common-anode and common-cathode, rather than one that lies about which
  it is.

Net effect: **1776 files, 1345 tiles.** SparkFun · Passives went from 68
squares to 19; Core · Basic from 71 (seven of them diodes, five of them 220 Ω
resistors) to 25.

Each part also carries an `order` — Fritzing's curated `.fzb` bin sequence
first, then family, then label — because the palette renders a bin as one flat
grid rather than a stack of family accordions.

## Packs

### SparkFun — 572 tiles in 14 packs

| id | name | tiles | files |
| --- | --- | --- | --- |
| `sparkfun-digitalic` | SparkFun · DigitalIC | 103 | 129 |
| `sparkfun-sensors` | SparkFun · Sensors | 84 | 92 |
| `sparkfun-connectors` | SparkFun · Connectors | 83 | 163 |
| `sparkfun-rf` | SparkFun · RF | 64 | 79 |
| `sparkfun-poweric` | SparkFun · PowerIC | 59 | 73 |
| `sparkfun-electromechanical` | SparkFun · Electromechanical | 51 | 112 |
| `sparkfun-analogic` | SparkFun · AnalogIC | 36 | 41 |
| `sparkfun-discretesemi` | SparkFun · DiscreteSemi | 29 | 52 |
| `sparkfun-passives` | SparkFun · Passives | 19 | 87 |
| `sparkfun-etc` | SparkFun · Etc. | 12 | 12 |
| `sparkfun-displays` | SparkFun · Displays | 10 | 18 |
| `sparkfun-led` | SparkFun · LEDs | 10 | 13 |
| `sparkfun-freqctrl` | SparkFun · Frequency Control | 9 | 21 |
| `sparkfun-boards` | SparkFun · Boards | 3 | 4 |

### Vendors — 219 tiles in 19 packs

| id | name | tiles | files |
| --- | --- | --- | --- |
| `arduino` | Arduino | 39 | 46 |
| `voltage-regulators` | Voltage Regulators | 22 | 28 |
| `picaxe` | PICAXE | 19 | 19 |
| `lilypad` | LilyPad | 18 | 18 |
| `analog-devices` | Analog Devices | 18 | 18 |
| `simulator` | Simulator Parts | 15 | 17 |
| `seeed` | Seeed Studio | 14 | 14 |
| `wemos` | WeMos | 12 | 12 |
| `atlas-scientific` | Atlas Scientific | 11 | 11 |
| `raspberry-pi` | Raspberry Pi | 9 | 10 |
| `infineon` | Infineon | 8 | 8 |
| `frc` | FRC | 8 | 8 |
| `intel` | Intel | 5 | 6 |
| `dagu` | Dagu | 5 | 7 |
| `spresense` | Spresense | 4 | 4 |
| `chipkit` | chipKIT | 4 | 4 |
| `esp` | ESP8266 / ESP32 | 4 | 4 |
| `calliope` | Calliope | 3 | 3 |
| `adafruit` | Adafruit | 1 | 1 |

## Regenerating

A full rebuild, from a tinyStudio checkout with `fritzing-parts` cloned
alongside:

```sh
node scripts/fritzing-import.mjs --src ../fritzing-parts --out /tmp/parts --all
node scripts/split-parts-packs.mjs \
  --src /tmp/parts \
  --out ../tinyparts \
  --bins ../fritzing-parts/bins \
  --migration src/renderer/src/assets/packMigration.json \
  --version 1.3.0
```

Add `--dry` to see the classification histogram without writing, and
`--show <pack-id>` to list what landed in a pack while tuning the rules.

To re-fold an existing checkout without re-importing anything — all you need
after a change to `scripts/lib/fold-variants.mjs` — use:

```sh
node scripts/refold-parts.mjs --repo ../tinyparts --all --bundle --dry
node scripts/refold-parts.mjs --repo ../tinyparts --all --bundle
node scripts/refold-parts.mjs --repo ../tinyparts --finish --bundle
```

Folding is per-pack, so `--packs a,b,c` splits the work up when `--all` is too
slow in one pass (the library is 125 MB, and these repos often sit on a network
or virtualised filesystem).

See tinyStudio's `docs/tinyparts-pack-setup.md` for the pack format and
publishing steps.

## License

Parts are derived from the Fritzing
[fritzing-parts](https://github.com/fritzing/fritzing-parts) library and remain
under **CC-BY-SA 3.0**. See each pack's `ATTRIBUTION.md`.
