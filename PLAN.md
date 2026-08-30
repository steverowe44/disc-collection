# Physical Disc Collection — Working Plan

Living record of Stephen's physical 4K UHD / Blu-ray / DVD collection.
This document is the spec. Read it before adding or editing any row in `collection.csv`.

## Files

| File | Purpose |
|---|---|
| `collection.csv` | The collection. Single source of truth. |
| `PLAN.md` | This spec — rules for filling the CSV. |
| `UNCERTAINTIES.txt` | Running log of every guessed field, one tickable item per doubt. |

`collection.csv` is UTF-8 **with BOM** so that Excel renders the `✓` character correctly.
Keep the BOM when rewriting the file.

## Columns

Exact header row, in order:

```
Collection,Name,Year,TV,Film,4K,Blu-ray,DVD,Label,Director,Notes
```

| Column | Rule |
|---|---|
| `Collection` | Name of the box set or TV series this row belongs to. **Left empty** when the release is standalone — most rows. Quote it if it contains a comma. |
| `Name` | Title with the leading article moved to the end, comma-separated: `Departed, The` / `Thing, The`. Alternate cuts go in the name in parentheses: `Blade Runner (Final Cut)`. TV seasons: `Sopranos, The: Season 3`. Because this field contains commas, it **must be quoted** in the CSV. |
| `Year` | Year the film / TV season **originally released** (theatrical or first broadcast). For an alternate cut, `original / cut` — e.g. Apocalypse Now Redux is `1979 / 2001`. A theatrical cut is just the original year. |
| `TV` | `✓` if the release is a TV show or season. Else empty. |
| `Film` | `✓` if the release is a film. Else empty. |
| `4K` | `✓` if the package contains a 4K UHD disc. Else empty. |
| `Blu-ray` | `✓` if the package contains a Blu-ray disc. Else empty. |
| `DVD` | `✓` if the package contains a DVD. Else empty. |
| `Label` | The company that produced/published the disc: `Arrow`, `Criterion`, `Second Sight`, `Lionsgate`, `Warner Bros.`, `88 Films`, `Eureka`, `Indicator`, `StudioCanal`, `Shout! Factory`, etc. Distributor of *this* release, not the production company of the film. |
| `Director` | Film: the director. TV: the **showrunner for that season**. Multiple names separated by `; ` (never a bare comma). |
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
6. Log every uncertainty to `UNCERTAINTIES.txt` **in the same commit**, then report back in chat:
   what was added, and a pointer to the new open items.

### Verifying online

Web search is available and should be used. When the exact release matters — which label,
which cut, whether a 4K package also contains a Blu-ray — look it up rather than guessing.
**blu-ray.com** is the best source for disc contents and release specs; Amazon UK listings
and the label's own site are reasonable backups.

Worth checking online, roughly in order of how often the guess would be wrong:

- Whether a 4K release includes a bundled Blu-ray (varies by title *and* territory).
- Which label put out a given title in this territory.
- Which cut a specific release contains.
- Original release year for anything obscure.

Knowledge of releases runs to **May 2026** — anything newer must be looked up, not recalled.

### Reporting uncertainty

Anything that goes into the CSV as a guess is written to `UNCERTAINTIES.txt` **in the same
commit as the rows it concerns**, so the doubt lives in git alongside the data instead of
scrolling away in chat.

Each item gets a stable ID (`U001`, `U002`...) and must contain enough to find the disc on
the shelf without hunting:

```
[ ] U001 | Image 04 | Row: "Thief" | Field: Label
    Where: top shelf, third from left, between Heat and The Insider.
           Black spine, red text, standard Blu-ray case.
    In CSV: Arrow
    Doubt:  Label logo not legible at this resolution.
    Need:   Read the logo at the bottom of the spine.
```

Always: image number, physical position and neighbours, what went in the CSV, what the doubt
is, and what would settle it. Same format whether the doubt is title, label, cut, year, or
disc contents.

When Stephen answers an item: correct `collection.csv`, tick the item, move it to the
RESOLVED section with the answer, and commit referencing the ID
(`Resolve U004: Thief is StudioCanal, not Arrow`). Resolved items are never deleted.

Never invent a title that isn't legible. An unreadable spine is logged as an unreadable
spine and gets no CSV row until it's identified.

### Future additions

Releases Stephen doesn't own yet get added the same way, to the same standard —
look up the release, fill every column, commit as its own batch.
