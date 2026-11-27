# StrikerAPI season pack

The last completed season of soccer as CSV, free to use under the [StrikerAPI Sample Data Licence](../DATA-LICENSE.md). Use it in anything you build, credit StrikerAPI, and don't republish the files themselves.

**Download:** the latest `strikerapi-season-pack.zip` from this repository's [season-pack release](https://github.com/sports-data-hq/strikerapi-mcp/releases/tag/season-pack). `preview/` holds the first 100 rows of a few files.

Seasons: 2025, 2026. Season naming: a season that spans two years carries its first year, so 2025 is 2025-26; MLS, NWSL, Liga MX, Brazil and Argentina use the calendar year. Competitions, by their API id: argentina, brazil, bundesliga, championship, eredivisie, laliga, ligamx, ligue1, mls, nwsl, primeira, scotland, seriea, soccer, ucl, uecl, uel (soccer is the Premier League). No odds, props or play-by-play: those, every earlier season and live data are on the API, free to start at https://strikerapi.com/signup?utm_source=github&utm_medium=pack&utm_campaign=strikerapi-pack.

## Files

| File | Rows |
|---|---|
| `strikerapi-argentina-2025-games.csv` | 510 |
| `strikerapi-argentina-2025-players.csv` | 1,070 |
| `strikerapi-argentina-2025-standings.csv` | 60 |
| `strikerapi-argentina-2025-teams.csv` | 30 |
| `strikerapi-brazil-2025-games.csv` | 380 |
| `strikerapi-brazil-2025-players.csv` | 760 |
| `strikerapi-brazil-2025-standings.csv` | 20 |
| `strikerapi-brazil-2025-teams.csv` | 20 |
| `strikerapi-bundesliga-2025-games.csv` | 306 |
| `strikerapi-bundesliga-2025-players.csv` | 507 |
| `strikerapi-bundesliga-2025-standings.csv` | 18 |
| `strikerapi-bundesliga-2025-teams.csv` | 18 |
| `strikerapi-championship-2025-games.csv` | 557 |
| `strikerapi-championship-2025-players.csv` | 791 |
| `strikerapi-championship-2025-standings.csv` | 24 |
| `strikerapi-championship-2025-teams.csv` | 24 |
| `strikerapi-eredivisie-2025-games.csv` | 307 |
| `strikerapi-eredivisie-2025-players.csv` | 550 |
| `strikerapi-eredivisie-2025-standings.csv` | 18 |
| `strikerapi-eredivisie-2025-teams.csv` | 18 |
| `strikerapi-laliga-2025-games.csv` | 380 |
| `strikerapi-laliga-2025-players.csv` | 611 |
| `strikerapi-laliga-2025-standings.csv` | 20 |
| `strikerapi-laliga-2025-teams.csv` | 20 |
| `strikerapi-ligamx-2025-games.csv` | 340 |
| `strikerapi-ligamx-2025-players.csv` | 657 |
| `strikerapi-ligamx-2025-standings.csv` | 36 |
| `strikerapi-ligamx-2025-teams.csv` | 18 |
| `strikerapi-ligue1-2025-games.csv` | 305 |
| `strikerapi-ligue1-2025-players.csv` | 561 |
| `strikerapi-ligue1-2025-standings.csv` | 18 |
| `strikerapi-ligue1-2025-teams.csv` | 18 |
| `strikerapi-mls-2025-games.csv` | 540 |
| `strikerapi-mls-2025-players.csv` | 886 |
| `strikerapi-mls-2025-standings.csv` | 30 |
| `strikerapi-mls-2025-teams.csv` | 30 |
| `strikerapi-nwsl-2025-games.csv` | 189 |
| `strikerapi-nwsl-2025-players.csv` | 369 |
| `strikerapi-nwsl-2025-standings.csv` | 14 |
| `strikerapi-nwsl-2025-teams.csv` | 14 |
| `strikerapi-nwsl-2026-games.csv` | 247 |
| `strikerapi-nwsl-2026-players.csv` | 421 |
| `strikerapi-nwsl-2026-standings.csv` | 16 |
| `strikerapi-nwsl-2026-teams.csv` | 16 |
| `strikerapi-primeira-2025-games.csv` | 306 |
| `strikerapi-primeira-2025-players.csv` | 579 |
| `strikerapi-primeira-2025-standings.csv` | 18 |
| `strikerapi-primeira-2025-teams.csv` | 18 |
| `strikerapi-scotland-2025-games.csv` | 228 |
| `strikerapi-scotland-2025-players.csv` | 389 |
| `strikerapi-scotland-2025-standings.csv` | 12 |
| `strikerapi-scotland-2025-teams.csv` | 12 |
| `strikerapi-seriea-2025-games.csv` | 380 |
| `strikerapi-seriea-2025-players.csv` | 609 |
| `strikerapi-seriea-2025-standings.csv` | 20 |
| `strikerapi-seriea-2025-teams.csv` | 20 |
| `strikerapi-soccer-2025-games.csv` | 380 |
| `strikerapi-soccer-2025-players.csv` | 551 |
| `strikerapi-soccer-2025-standings.csv` | 20 |
| `strikerapi-soccer-2025-teams.csv` | 20 |
| `strikerapi-ucl-2025-games.csv` | 189 |
| `strikerapi-ucl-2025-players.csv` | 884 |
| `strikerapi-ucl-2025-standings.csv` | 36 |
| `strikerapi-ucl-2025-teams.csv` | 36 |
| `strikerapi-uecl-2025-games.csv` | 153 |
| `strikerapi-uecl-2025-players.csv` | 891 |
| `strikerapi-uecl-2025-standings.csv` | 36 |
| `strikerapi-uecl-2025-teams.csv` | 36 |
| `strikerapi-uel-2025-games.csv` | 189 |
| `strikerapi-uel-2025-players.csv` | 927 |
| `strikerapi-uel-2025-standings.csv` | 36 |
| `strikerapi-uel-2025-teams.csv` | 36 |

## Columns

- **games**: `league`, `season`, `stage`, `date`, `home_team`, `away_team`, `home_score`, `away_score`, `home_shootout`, `away_shootout`, `neutral_site`
- **players**: `league`, `season`, `player`, `team`, `apps`, `starts`, `minutes`, `goals`, `assists`, `shots`, `shots_on_target`, `yellow_cards`, `red_cards`, `saves`, `penalty_goals`, `xg`, `np_xg`
- **standings**: `league`, `season`, `group_name`, `rank`, `team`, `played`, `wins`, `draws`, `losses`, `goals_for`, `goals_against`, `goal_diff`, `points`, `deductions`
- **teams**: `league`, `season`, `team`, `games`, `goals_for`, `goals_against`, `xg_for`, `xg_against`, `elo_end_of_season`

Credit line for anything you publish: **Data: StrikerAPI (strikerapi.com)**
