
# Serum 2 JSON Preset Spec  — Project *SPU*

_Last generated 2025-04-25_

## Folder layout
| Path | Contents |
|------|----------|
| `mapping/osc.json` | Alias → ID table for Oscillators & WTOsc parameters |
| `mapping/filter.json` | Filter 1 / Filter 2 parameter IDs |
| `mapping/env_lfo.json` | Env 1‑4 & LFO 1‑10 IDs |
| `mapping/macro_lfo_bus.json` | Macro 1‑8 & LFO‑Bus 1‑16 IDs |
| `mapping/routing_matrix.json` | FX‑bus / filter-balance routing slots |
| `mapping/noise_sub_osc.json` | Noise OSC & Sub OSC parameters |
| `mapping/global.json` | Global panel & voice‑panel scaling |
| `mapping/source_id.csv` | **Source aliases → Source IDs** for the Mod Matrix |
| `examples/init.json` | “Init” preset from Serum 2.0.16 (golden defaults) |

## Default‑value rule
*Any parameter not present in a `plainParams` object is assumed to be factory‑default.*  
For unused modules, keep `"plainParams": "default"` to match Init.

## Alias syntax
* **Destinations** in a Mod‑Slot use the exact human label found in these maps  
  (e.g. `"Filter 1 Freq"`, `"Osc A Level"`).
* **Sources** use the `Readable Source Name` column in `source_id.csv` (e.g. `"LFO 1"`, `"Macro 4"`).

## Example change‑set (English → JSON)
```jsonc
{
  "meta": { "name": "NightWub", "author": "Shiraz" },
  "osc": { "A": { "octave": -1, "warp": ["Sync", 0.4] } },
  "filter": { "type": "MgL12", "freq": 0.25, "reso": 25 },
  "fx": [
    { "type": "Distortion",
      "params": { "mode": "Tube", "drive": 25, "freq": 425, "q": 1.9, "wet": 100 } }
  ],
  "matrix": [
    { "source": "LFO 1", "dest": "Filter 1 Freq", "amount": 80 }
  ]
}
```

A patcher script will read these aliases, look them up in `mapping/*`, apply the deltas to `examples/init.json`, and write a **Serum‑ready** preset.

---
Pull requests welcome!
