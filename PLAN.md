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
Name,Year,Disc Year,TV,Film,4K,Blu-ray,DVD,Label,Director,Collection
```

| Column | Rule |
|---|---|
| `Name` | Title with the leading article moved to the end, comma-separated: `Departed, The` / `Thing, The` / `Bout de souffle, À`. Because this contains a comma, the field **must be quoted** in the CSV. Keep the title as it appears on the release. |
| `Year` | Year the **film or TV show/season originally released** (theatrical / first broadcast), not the disc. 4 digits. |
| `Disc Year` | Year **this particular disc release** was published. For a box set, the year of the box set. |
| `TV` | `✓` if the release is a TV show or season. Else empty. |
| `Film` | `✓` if the release is a film. Else empty. |
| `4K` | `✓` if the package contains a 4K UHD disc. Else empty. |
| `Blu-ray` | `✓` if the package contains a Blu-ray disc. Else empty. |
| `DVD` | `✓` if the package contains a DVD. Else empty. |
| `Label` | The company that produced/published the disc: `Arrow`, `Criterion`, `Second Sight`, `Lionsgate`, `Warner Bros.`, `88 Films`, `Eureka`, `Indicator`, `StudioCanal`, `Shout! Factory`, etc. Distributor of *this* release, not the production company of the film. |
| `Director` | Film: the director. TV: the **showrunner for that season**. Multiple names separated by `; ` (never a bare comma). |
| `Collection` | Name of the box set this row belongs to, if any. Empty for standalone releases. |

### The `✓` rule (important)

`TV`, `Film`, `4K`, `Blu-ray`, `DVD` are **independent boolean columns**.

- True  → the single Unicode character `✓` (U+2713).
- False → **completely empty cell**. Never `✗`, `x`, `N`, `0`, `-`, or a space.

A release containing both a 4K and a Blu-ray gets `✓` in **both** the `4K` and `Blu-ray` columns.

## Rules

### Box sets

One row **per film**, not per box. A three-film box set produces three rows.
Every row carries the box set's name in `Collection`.

- `Year` is each film's own release year (they will differ across rows).
- `Disc Year` is the box set's release year (same on every row).
- `Director` is each film's own director.
- Disc-format ticks reflect **what that specific film ships as** inside the box where that is known
  (e.g. a set where only one film got a 4K → only that row gets a `4K` tick). If the whole box
  is uniformly one format, all rows match.
- Do **not** add a separate summary row for the box set itself.

### TV shows

One row **per season**, with `Collection` naming the show/box.

- Single-season release → one row, `Collection` empty unless it came in a larger set.
- Complete-series set → one row per season, `Collection` = the set's name
  (e.g. `Sopranos, The: The Complete Series`).
- Put the season in the `Name` field: `Sopranos, The: Season 3`.
- `Year` = the year that **season** first aired.
- `Director` = that season's showrunner.

### Duplicate films

Multiple copies of the same film are **separate rows**, always. Never merge, never dedupe.
Example: a *Blade Runner: The Final Cut* 4K+BD combo and a *Blade Runner: The Final Cut*
Blu-ray steelbook are two independent rows.
Where two rows would otherwise be identical, the `Label` / `Disc Year` should distinguish them;
if they still don't, flag it and ask rather than silently merging.

### Sorting

Rows are kept in the order they were added (chronological by entry batch), **not** alphabetically.
Do not re-sort the file — it makes diffs unreadable. Sorting is the viewer's job.

## Workflow

1. Stephen posts images of shelves / spines / individual cases in chat.
2. Read every title visible in the image.
3. For each release, determine the fields above. What the image can't tell us
   (director, original release year, label, exact disc contents) is filled from knowledge of the
   release; where the specific edition is genuinely ambiguous, **ask rather than guess**.
4. Append rows to `collection.csv`.
5. **Commit that batch** with a message naming what was added, e.g.
   `Add 14 titles from shelf photo 3 (Arrow / Criterion)`.
   One commit per batch, so any batch can be reverted independently.
6. Report back: what was added, and anything that needed a judgement call or is unverified.

### Accuracy

- Never invent a title that isn't legible. If a spine is unreadable, say so and skip it.
- If confidence on a field is low, state it in the reply — don't bury an uncertain value in the CSV.
- Prefer the specific edition's details over generic ones: the same film has many releases with
  different labels, years, and disc contents.

### Future additions

Releases Stephen doesn't own yet get added the same way, to the same standard —
look up the release, fill every column, commit as its own batch.
