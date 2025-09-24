# Tongue-Click Synth · Toki Pona Edition

A tiny web app that turns text into a **tongue-click sound language** and (optionally) shows **Sitelen Pona** glyphs. It’s a playful prototype for exploring non-verbal sound patterns, rhythm, and symbol→sound mappings.

> Made for fun. An experiment. **Not** an alien language… *(maybe).* 👽

---

## ✨ What it does

* **Click Synth:** generates percussive “tongue click” patterns from text.
* **Three input modes:**

  * **Toki Pona** (Latin words → click patterns + Sitelen Pona glyphs)
  * **Click Kana** (your 5×5 “syllable grid”)
  * **Letters** (A–Z → rhythmic clicks)
* **Symbols Browser:** search a Toki Pona word list, hover to see a quick gloss, click to insert & play.
* **Sitelen Pona preview:** Latin → **UCSUR** (Private-Use) codepoints → rendered via a local font (e.g., **nasin nanpa** UCSUR build).
* **Safe fallback:** if a string has no UCSUR chars, preview stays Latin—no tofu boxes.

---

## 🚀 Quick start

1. **Clone / download** this project.
2. Put the Sitelen Pona font in `./fonts/`, e.g.:

   * `./fonts/nasin-nanpa-5.0.0-beta.2-UCSUR.otf`
3. Run a tiny local server from the project folder:

   ```bash
   # Python
   python -m http.server 8000
   # Node
   npx http-server -p 8000
   ```
4. Open `http://localhost:8000/` and load the HTML file.
5. Type: `toki pona li pona! jan li toki.` → Play.

---

## 🔊 The click sound (how it’s synthesized)

Clicks are **short noise bursts** shaped with envelopes and filters:

* **Source:** white noise buffer with a quick decay.
* **Envelope:** fast attack + fast exponential decay (percussive).
* **Coloring:** `BiquadFilterNode` (high-pass/band-pass) to make distinct timbres:

  * **dental** = crisp/high (front-palate)
  * **side (molar)** = mid band
  * **back-palate** = deeper/low band
  * **cheek burst** = two quick mid bursts in succession
  * **slur** = band-pass sweep (a brief “slide” between clicks)
* **Tempo:** UI control sets base unit (ms), each symbol advances by a multiple of that unit.

> Hearing safety: the master gain is capped, but still **start low** and increase carefully, especially on headphones.

---

## 🔤 Phonetic pattern (the “Click Kana”)

Six symbols define the building blocks:

| Symbol | Meaning      | Example sound role               |
| -----: | ------------ | -------------------------------- |
|    `·` | dental click | short, bright, forward           |
|    `~` | slur         | brief swept noise, connects hits |
|    `>` | side/molar   | mid, wider                       |
|    `^` | back-palate  | deeper, rounder                  |
|    `=` | cheek burst  | double pop                       |
|    `—` | long click   | sustained accent (click + slur)  |

**5×5 mapping** (consonant row × vowel column):

```
       A     E      I      O      U
K:   ·~    ·~·    ·~~    ·^~    ·>~
T:   ··~   ··~·   ··~~   ··^~   ··>~
S:    >~    >~·    >~~    >^~    >>~
M:    =~    =~·    =~~    =^~    =>~
N:    ^~    ^~·    ^~~    ^^~    ^>~
```

Write tokens like `TA KU / KO SU` or `KAKEKI`. Spaces and `/` create gaps in rhythm.

---

## 🧩 Toki Pona integration

### Syllabification → Click patterns

* Words are parsed into **(C)V** syllables with an optional final **n**.
* Consonant rows are approximated to the 5 rows: **k, t, s, m, n**.
  **Aliases:** `p→k`, `l→s`, `w→m`, `j→s`.
* Bare vowels map to default slur-ish vowels:
  `a→·~`, `e→~·`, `i→~~`, `o→^~`, `u→>~`.
* A **lone ‘n’** (word-final or before non-vowel) adds a short **nasal marker** `^`.

Example:

```
toki pona → to-ki  po-na
  to → K O → ·^~
  ki → K I → ·~~
  po → P O → (p→k) K O → ·^~
  na → N A → ^~
+ nasal finals if present
```

### Sitelen Pona preview (UCSUR)

* Latin tokens are converted to **UCSUR Private-Use codepoints** for Sitelen Pona display.
* Rendering uses a **local UCSUR font** (e.g., *nasin nanpa* UCSUR build).
* If a token doesn’t have a UCSUR mapping, the preview stays **Latin**.

**Local font setup:**

```css
@font-face{
  font-family:"NasinNanpa";
  src:url("./fonts/nasin-nanpa-5.0.0-beta.2-UCSUR.otf") format("opentype");
  font-display: swap;
}
.preview { font-family:"NasinNanpa", "Segoe UI Symbol", "Noto Sans Symbols2", sans-serif; }
```

**Why a special font?** Sitelen Pona isn’t in official Unicode; communities use a **PUA (UCSUR)** range. Fonts map those codepoints to glyphs.

---

## 🕹️ UI tips

* **Mode:** Toki Pona / Click Kana / Letters
* **Tempo:** millisecond unit per symbol (lower = faster)
* **Volume:** master gain (the synth uses dynamic envelopes)
* **Symbols panel:** hover a word for a short gloss; click to insert & auto-play
* **Shortcuts:**

  * **Enter** = Play (Cmd/Ctrl+Enter always plays)
  * **Esc** = Stop

---

## 🐞 Troubleshooting

* **Boxes instead of glyphs?**

  * Make sure you’re using a **UCSUR** build of the font.
  * Serve over **[http://localhost](http://localhost)** (not `file://`).
  * Confirm the preview actually contains **PUA** characters (Latin won’t render as glyphs without conversion).
* **No sound?**

  * Some browsers require a user gesture—click **Play** once.
  * Check system volume / audio output device.
* **Rhythm too dense/thin?**

  * Adjust **Tempo** (ms/unit).
  * Use spaces or `/` to add rests.

---

## 📦 Tech notes

* **Audio:** Web Audio API—noise buffer, biquad filters, and simple ADSR.
* **Parser:** Regular expressions + simple syllable scanner.
* **Rendering:** Vanilla JS; no external frameworks required.
* **Fonts:** Local `@font-face`; safe fallback to Latin when no PUA.

---

## 🧪 Why this exists

Curiosity! Exploring sound as **symbolic rhythm**—how much information can we convey with clicks, slurs, and timing alone? Toki Pona’s simple phonotactics make it a great playground for **sound-symbol alignment**.

> Built just for fun. An experiment. **Not** an alien language… *(unless the aliens email us first).* 😉

---

## 📜 License

Use, remix, and tinker freely. If you need a formal license, go with **MIT**.

---

## 🙏 Credits

* Sitelen Pona community and type designers (e.g., *nasin nanpa* font).
* Toki Pona creators and contributors for the language & learning resources.

---

## 🗺️ Roadmap (optional ideas)

* WOFF2 font + `preload` for faster loads
* PWA/offline caching
* WAV/MP3 export (MediaRecorder)
* Haptic feedback on mobile clicks
* Theme/skin switcher, font selector (e.g., *linja pona*)
* Practice mode (hear pattern → guess word)


