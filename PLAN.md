# Physical Disc Collection — Working Plan

Living record of Stephen's physical 4K UHD / Blu-ray / DVD collection.
This document is the spec. Read it before adding or editing any row in `collection.csv`.

## Files

| File | Purpose |
|---|---|
| `collection.csv` | The collection. Single source of truth. |
| `PLAN.md` | This spec — rules for filling the CSV. |

`collection.csv` is UTF-8 **with BOM** so that Excel renders the `✓` character correctly.
Keep the BOM when rewriting the file.

## Columns

Exact header row, in order:

```
Name,Year,TV,Film,4K,Blu-ray,DVD,Label,Director,Collection,Notes
```

| Column | Rule |
|---|---|
| `Name` | Title with the leading article moved to the end, comma-separated: `Departed, The` / `Thing, The`. Alternate cuts go in the name in parentheses: `Blade Runner (Final Cut)`. TV seasons: `Sopranos, The: Season 3`. Because this field contains commas, it **must be quoted** in the CSV. |
| `Year` | Year the film / TV season **originally released** (theatrical or first broadcast). For an alternate cut, `original / cut` — e.g. Apocalypse Now Redux is `1979 / 2001`. A theatrical cut is just the original year. |
| `TV` | `✓` if the release is a TV show or season. Else empty. |
| `Film` | `✓` if the release is a film. Else empty. |
| `4K` | `✓` if the package contains a 4K UHD disc. Else empty. |
| `Blu-ray` | `✓` if the package contains a Blu-ray disc. Else empty. |
| `DVD` | `✓` if the package contains a DVD. Else empty. |
| `Label` | The company that produced/published the disc: `Arrow`, `Criterion`, `Second Sight`, `Lionsgate`, `Warner Bros.`, `88 Films`, `Eureka`, `Indicator`, `StudioCanal`, `Shout! Factory`, etc. Distributor of *this* release, not the production company of the film. |
| `Director` | Film: the director. TV: the **showrunner for that season**. Multiple names separated by `; ` (never a bare comma). |
| `Collection` | Name of the box set this row belongs to, if any. Empty for standalone releases. |
| `Notes` | Free text. Anything the schema doesn't cover — packaging, edition, condition, who borrowed it, uncertainty about a field. Quote it if it contains a comma. |

There is deliberately **no** disc-release-year column and **no** cut column.

### The `✓` rule (important)

`TV`, `Film`, `4K`, `Blu-ray`, `DVD` are **independent boolean columns**.

- True  → the single Unicode character `✓` (U+2713).
- False → **completely empty cell**. Never `✗`, `x`, `N`, `0`, `-`, or a space.

A release containing both a 4K and a Blu-ray gets `✓` in **both** the `4K` and `Blu-ray` columns.

## Rules

### Box sets

One row **per film**, not per box. A three-film box set produces three rows.
Every row carries the box set's name in `Collection`.
The box set itself never gets its own row.

- `Year` is each film's own release year (they will differ across rows).
- `Director` is each film's own director.
- Disc-format ticks reflect **what that specific film ships as** inside the box where that is known
  (e.g. a set where only one film got a 4K → only that row gets a `4K` tick). If the whole box
  is uniformly one format, all rows match.

### TV shows

One row **per season**, always — including complete-series sets.

- Season goes in the `Name`: `Sopranos, The: Season 3`.
- Complete-series set → one row per season, `Collection` = the set's name
  (e.g. `Sopranos, The: The Complete Series`).
- Single-season release → one row, `Collection` empty unless it came in a larger set.
- `Year` = the year that **season** first aired.
- `Director` = that season's showrunner.

### Multiple cuts

Each cut of a film is its own row. Apocalypse Now with all three versions owned =
three rows: `Apocalypse Now`, `Apocalypse Now (Redux)`, `Apocalypse Now (Final Cut)`.

- The cut is named in `Name`, in parentheses. There is no separate cut column.
- `Year` carries both years, `original / cut`: `1979 / 2001`, `1979 / 2019`.
- This applies whether the cuts came in one package or were bought separately.
  If one disc holds several cuts, that's still one row per cut.

### Duplicate films

Multiple copies of the same film are **separate rows**, always. Never merge, never dedupe.
Example: a *Blade Runner (Final Cut)* 4K+BD combo and a *Blade Runner (Final Cut)*
Blu-ray steelbook are two independent rows.
Where two rows would otherwise read identically, use `Label` and `Notes` to tell them apart.

### Sorting

Rows are kept in the order they were added (chronological by entry batch), **not** alphabetically.
Do not re-sort the file — it makes diffs unreadable. Sorting is the viewer's job.

## Workflow

1. Stephen posts images of shelves / spines / individual cases in chat.
   **Each image has an image number written in its bottom-right corner.**
2. Read every title visible in the image.
3. For each release, determine the fields above. What the image can't tell us
   (director, original release year, label, exact disc contents) is filled from knowledge of
   the release.
4. Append rows to `collection.csv`.
5. **Commit that batch** with a message naming what was added, e.g.
   `Add 14 titles from image 03 (Arrow / Criterion)`.
   One commit per batch, so any batch can be reverted independently.
6. Report back: what was added, plus every uncertainty (see below).

### Reporting uncertainty

Best guess goes in the CSV, and the guess is reported so Stephen can correct it fast.
Every uncertainty report must cite the **image number** and give enough physical detail to
locate the disc on the shelf without hunting:

> Image 04 — between *Heat* and *The Insider*, black spine with red text, top shelf.
> My guess: *Thief* (Arrow). Couldn't read the label from the spine.

Cover the case, the neighbours, the colour/position, then the guess and what was uncertain.
Same format whether the doubt is about the title, the label, the cut, or the disc contents.
Never invent a title that isn't legible — if a spine is unreadable, report it as unreadable
and skip the row rather than guessing blind.

### Future additions

Releases Stephen doesn't own yet get added the same way, to the same standard —
look up the release, fill every column, commit as its own batch.
