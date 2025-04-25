half this is wrong

# Serum 2 Preset Dev Kit

*Author: SLASR · Last updated: 2025‑04‑24*

---

## 1 · What is this?

A **single‑source reference** for building, validating, and iterating Serum 2 preset files entirely in JSON.  It contains:

| Folder               | What you’ll find                                                                                                                                                     |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `mapping/`           | Machine‑readable tables that translate **human labels** → `(Module Type, Module ID, Param ID)` for every destination knob, plus source‑ID tables for the Mod Matrix. |
| `examples/`          | The untouched `init.json` direct from Serum 2.0.16 and other demo presets.                                                                                           |
| `SerumPresetSpec.md` | Deep‑dive spec (format rules, default‑value convention, alias grammar, change‑set schema).                                                                           |

Keep this repo in **any** of your projects; ChatGPT can fetch the raw files on demand, so you never have to re‑upload huge JSON blobs in a new session.

---

## 2 · Quick‑start workflow

1. Start from `examples/init.json` → all modules are declared, every unused block reads `"plainParams": "default"`.
2. Write a **prompt** or **change‑set** (plain English is fine) describing what to change, e.g.
   > “Dubstep growl: Osc A at –1 oct with Sync warp 0.4; Tube distortion drive 35 %, wet 100 %; Filter 1 MG12 at 300 Hz with LFO 1 wobble +80.”
3. ChatGPT (or the local patcher script) looks up every alias in `mapping/`, injects only the non‑default keys, and writes `MyPreset.serumpreset`.
4. Load in Serum — tweak — repeat.

---

## 3 · Core rules Serum **will** choke on if you break them

| Rule                                                                                                      | Why it matters                                                                        |
| --------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Use** `"plainParams": "default"` for any module you haven’t modified.                                   | Serum ignores blocks with that literal string; numeric zeros can crash or mute sound. |
| **Omit** parameters still at their factory value.                                                         | Smaller file; prevents Serum from mis‑parsing extraneous keys.                        |
| **Nested blocks must stay nested.** E.g. `Oscillator0 → WTOsc0` — never hoist a WTOsc block to top‑level. | Serum loader won’t find it otherwise.                                                 |
| **Mod Matrix rows need *****both***** Source & Dest IDs.**                                                | A half‑filled slot shows “— — —” and does nothing.                                    |

Full table of defaults & special cases lives in `SerumPresetSpec.md`.

---

## 4 · Alias → ID mapping cheat‑sheet

*(See mapping/*.json for the authoritative list)\*

| Alias example   | Module Type | Module ID | Param ID |
| --------------- | ----------- | --------- | -------- |
| `Osc A Level`   | Oscillator  | 0         | 1        |
| `Osc A WT Pos`  | WTOsc       | 0         | 6        |
| `Filter 1 Freq` | VoiceFilter | 0         | 2        |
| `Env 1 Attack`  | Env         | 0         | 0        |
| `LFO 3 Rate`    | LFO         | 2         | 0        |
| `Macro 6`       | Macro       | 5         | 0        |

*Source aliases* (for Matrix) are in `mapping/source_id.csv`.

---

## 5 · Example change‑set JSON (optional)

```jsonc
{
  "meta": { "name": "NightWub 01", "author": "Shiraz" },
  "osc": {
    "A": { "octave": -1, "warp": ["Sync", 0.4] }
  },
  "filter": { "type": "MgL12", "freq": 0.25, "reso": 25 },
  "fx": [
    { "type": "Distortion",
      "params": { "mode": "Tube", "drive": 35, "freq": 425, "q": 1.9, "wet": 100 } }
  ],
  "matrix": [
    { "source": "LFO 1", "dest": "Filter 1 Freq", "amount": 80 }
  ]
}
```

Feed this to `patcher.py` (WIP) and you get a valid preset.

---

## 6 · TODO / PR welcome

- Populate FX parameter maps for Chorus, Phaser, Flanger, etc.
- Add script examples in Python & C++ (JUCE).
- CI test that every alias in `mapping/*` resolves to **one** unique ID.

---

