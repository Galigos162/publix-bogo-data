# publix-bogo-data

The data behind publixbogotracker.com: weekly deal CSVs, the item catalog, store tables, and product images. It is data only; there are no scripts here. The website (`Galigos162/publix-bogo-tracker`) and its weekly email job fetch these files at runtime from `https://raw.githubusercontent.com/Galigos162/publix-bogo-data/main/`.

The full picture of how the three repos fit together is in `PROJECT_OVERVIEW.md` in `publix-bogo-tracker`.

## Rules

- **This repo is public.** Never commit personal data (emails, user IDs, anything from Firestore), keys, or tokens.
- **Anything pushed to `main` is live on the site immediately.** There's no build step or staging. Renaming a folder, file, or column breaks the site and the email job. Those contracts are documented in `publix-bogo-tracker/CLAUDE.md`, so change them there and in the code in the same effort.
- **Most files are generated.** They're written by the scripts in `Galigos162/publix-api-scrape-python` (`version2/`), which run on Denis's machine against this repo's local clone and are pushed as "Weekly BOGO update YYYY-MM-DD" commits (usually twice a day, Tue–Thu). Hand edits to generated files get overwritten by the next run; fix the script or the source file instead.
- **The pipeline writes into the local clone and pushes `main`.** On the machine that runs it, keep that clone on `main` with no uncommitted work. For doc or other changes there, use a separate `git worktree` or clone.

## Files

| Path | Columns | Written by | Read by |
|---|---|---|---|
| `weekly-v2/WeeklyBOGOv2-allSections-{storeNumber}-mod.csv` (one per store) | `name,imgSrc,categories,dealType,digitalCoupon,isSelected` | `check-new-bogo-items-v2.py` | site, email job |
| `all-items/all_items.csv` (master item list) | `name,imgSrc,category,isSelected` | `check-new-bogo-items-v2.py` appends new items and fills blank categories; hand edits are OK | site, scraper scripts |
| `images/` | — | `check-new-bogo-items-v2.py` downloads images for new items | site, email job (via `imgSrc`) |
| `cutover-days/store_cutover_days.csv` | `storenumber,cutoverDay,validFrom,validTo` | `csv-formatting-v2.py` | site, email job |
| `store-locations/zip_code_main_table.csv` | `zipcode,storenumber,name,address,city,state,shortname` | `location-api-publix-request.py`, run occasionally in zip batches and merged by hand | site, email job |
| `name-aliases/name_aliases.csv` | `canonical,variant` | Edited by hand after reviewing `find_duplicate_candidates.py` output | scraper scripts |

Format details that trip people up:
- Weekly files: `imgSrc` is a full raw GitHub URL into `images/`. `categories` can hold several values separated by `|`. `dealType` is `BOGO`, `B2G1`, `B2G2` or `B2G3`. `isSelected` is lowercase `false`.
- `all_items.csv` differs from the weekly files: `imgSrc` is a bare filename, the column is `category` (singular), and `isSelected` is uppercase `FALSE`. The scripts rewrite it with an atomic temp-file swap, so if you edit it by hand, keep every row the same length.
- Product images are lowercase snake_case slugs of the item name with punctuation removed (e.g. `natures_own_butterbread.jpg`). Category icons are kebab-case `.png` files (e.g. `beer-and-wine.png`).
- `name_aliases.csv` uses CRLF line endings; the other files use LF.
- `cutoverDay` is `wednesday` or `thursday`. `validFrom`/`validTo` are blank for a few stores that haven't been scraped yet.
