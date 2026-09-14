# tinyparts

The parts library for [tinyStudio](https://github.com/Mister-Industries/tinyStudio).
Every component the Circuit view can place lives here as plain files, and the
art is real `.svg` files you can open in Illustrator.

**Full guide:** tinyStudio's [`docs/parts-and-art.md`](https://github.com/Mister-Industries/tinyStudio/blob/main/docs/parts-and-art.md).

## Where things are

```
index.json                  every pack; "bundled": true = compiled into tinyStudio
packs/
  tinyboards/               the tinyBoard family (built into tinyStudio)
    pack.json
    parts/tinycore/
      part.json             name, real size (1.9in), pin names
      breadboard.svg        ← the board art: edit it in Illustrator
      icon.svg              ← its palette tile
    parts/tinyglow/ …
  core/                     everyday parts (built into tinyStudio)
    parts/resistor/         part.json + breadboard.svg + schematic.svg (+ icon.svg)
  sparkfun-*/, arduino/, …  optional packs, installed from the app
    parts/<type>.json       one file per part with the SVG embedded (see below)
```

## Editing art

- Open a part's `.svg` in Illustrator and change whatever you like.
- **Pins are the shapes named `pin-<NAME>`** (`pin-GND`, `pin-D8`, `pin-A.5`…),
  placed at the shape's centre. Move them freely, but don't rename them: saved
  circuits connect to pins by name.
- Export with **Object IDs: Layer Names** so those names survive.
- To see your changes live, run `npm run dev` (or `npm run dev:web`) in
  tinyStudio with this repo cloned next to it. The app reads parts straight from
  here and reloads them on every save.

## How changes reach people

- **Push to `main`.** Every copy of tinyStudio checks GitHub when it starts (at
  most every 15 minutes) and downloads only the files that changed. No app
  release is needed. Parts Packs → *Check for updates* checks right away.
- `tinyboards` and `core` are also compiled into the app, so they work offline.
  Before a tinyStudio release, run `npm run parts:sync` there and commit the
  result.
- To preview a branch, run
  `localStorage.setItem('tinystudio.tinyparts.source', 'Mister-Industries/tinyparts@<branch>')`
  in the app's DevTools console.

## Tools (run from tinyStudio)

```sh
npm run parts:check                  # validate this checkout with the app's own loader
npm run parts:check -- --fix         # …and repair pack.json / index.json listings
npm run parts:new -- my-sensor --pack core --label "My Sensor"
node scripts/parts-tool.mjs explode --pack sparkfun-led   # make an optional pack editable
```

Run `parts:check` before pushing. A malformed part.json would otherwise reach
every install.

## Editing an optional pack

The SparkFun and vendor packs are generated from Fritzing, one JSON file per part
with the SVG embedded as a string. `explode` turns a pack (or `--only a,b`
parts of it) into folders of `part.json` + `.svg`, which the app reads the same
way. A single part also becomes a folder the first time you save it from the
Parts editor with **Save to tinyparts**.

## Why so many packs

There used to be a single `tinystudio-core` pack holding all 1776 parts: a
124 MB download to get one resistor, landing in the palette as one tab. The
library is split along the same lines Fritzing uses for its bins, so you
install only the bins you work with.

Parts are classified by, in order: explicit membership in a Fritzing `.fzb`
bin file, the `sparkfun-<subbin>-*` filename convention, vendor prefixes, then
keyword rules over family/label.

## Package variants

Fritzing ships one `.fzp` per *PCB package*, and the package is frequently the
only difference. SparkFun titles all sixteen of its inductors "Inductors", all
thirteen ceramic caps "Capacitor" and all eighteen electrolytics "Capacitor
Polarized". They differ in footprint, which is a view tinyStudio doesn't have.
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

Each part also carries an `order` — Fritzing's curated `.fzb` bin sequence
first, then family, then label.

## Packs

### tinyStudio: built in

| id | name | parts |
| --- | --- | --- |
| `tinyboards` | tinyBoards | 6 |
| `core` | Core | 29 |

### SparkFun: 572 tiles in 14 packs

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

### Vendors: 219 tiles in 19 packs

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

## Importing more Fritzing parts

`scripts/fritzing-import.mjs` in tinyStudio converts `.fzp` parts from a
[fritzing-parts](https://github.com/fritzing/fritzing-parts) checkout. Explode
its output into a pack:

```sh
node scripts/fritzing-import.mjs --src ../fritzing-parts --only resistor --views breadboard,schematic
node scripts/parts-tool.mjs explode --from tmp/fritzing-import --pack core
```

The scripts that originally split and folded the vendor packs
(`split-parts-packs.mjs`, `refold-parts.mjs`) aren't on tinyStudio's current
branches.

## License

The Fritzing-derived parts (the `core` pack and every SparkFun/vendor pack) come
from the [fritzing-parts](https://github.com/fritzing/fritzing-parts) library and
remain under **CC-BY-SA 3.0**; see each pack's `ATTRIBUTION.md`. The `tinyboards`
art was drawn for tinyStudio by MR.INDUSTRIES.
