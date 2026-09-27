# publix-bogo-data

Public data for the [Publix BOGO Tracker](https://www.publixbogotracker.com) website: this week's deals for every Publix store, the catalog of items that have been on deal, store and zip-code tables, and product images.

The site reads these files directly from GitHub at runtime, so a push to `main` updates the live site without a rebuild. The files are generated weekly by the scripts in `publix-api-scrape-python`.

| Folder | What's in it |
|---|---|
| `weekly-v2/` | One deals CSV per store: `WeeklyBOGOv2-allSections-{storeNumber}-mod.csv` |
| `all-items/` | `all_items.csv`, every item that has been on deal |
| `images/` | Product images and category icons |
| `cutover-days/` | `store_cutover_days.csv`: whether each store's week starts Wednesday or Thursday |
| `store-locations/` | `zip_code_main_table.csv`: zip code → nearby stores |
| `name-aliases/` | `name_aliases.csv`: maps variant item names to one canonical name |

Column details and editing rules are in [`CLAUDE.md`](CLAUDE.md). How the whole project fits together is in `PROJECT_OVERVIEW.md` in the `publix-bogo-tracker` repo.

Not affiliated with Publix Super Markets, Inc.
