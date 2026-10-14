# Pokémon Baseball — 10-Player Draft & 162-Game Simulator (v2.1)

This is a **runnable browser game with its own Node.js server**, based on the six JavaScript source files you supplied. It does NOT need npm dependencies or a database to run on your machine. The homepage, solo challenge, league lobbies, turn-validated snake draft, Pokémon browser, hover scouting reports, baseball diamond, standings and simulations are included. It is not a clone of the All-22 website.

## Start the game

1. Install Node.js 20+ from https://nodejs.org/.
2. Unzip this archive and open a terminal inside the extracted `pokemon_baseball_v2` directory. You should see `package.json`, `server.js`, and `engine.js` in that same directory.
3. Run `npm start` (or `node server.js`). No `npm install` is necessary.
4. Visit **http://localhost:3000** in your browser.
5. Click **Solo challenge**, or create a fantasy league, add computer teams to the empty seats and click **Start draft**.
6. Move Pokémon on the field by clicking two positions to swap them, or dragging from one to the other on desktop. Team ratings refresh with each move.

**To test with two people:** Invite a friend in another browser/private window to join the same league code. Both browsers must reach the SAME server. `localhost` is only accessible from your own device; to play on separate networks, publish the Node server on a public host.

## Draft rules

- Exactly **10 selections per team**, no bench: **C, 1B, 2B, 3B, SS, LF, CF, RF, DH, P**.
- Each team receives ten turns in a **snake draft**. Pokémon may be drafted only once in a league. Every selected Pokémon initially goes to an empty position, then can be swapped with your other Pokémon before lineups are locked.
- League settings: 3–6 teams; all nine generations, Kanto, Retro (Gen 1–4), or each generation alone; legend eligibility; private/public lobby; random/host-selected draft order; no clock or 30/60/120-second pick timer.
- CPU opponents select Pokémon and auto-pick for an expired human clock. Host can add CPUs, move or remove teams, change settings before starting, and start the season after all rosters are full. Chat and shiny (1/8192) events remain.
- The **entire available Pokémon pool** appears in a scrollable list in the side panel, with instant name search and sorting. Hover/focus any Pokémon, including a drafted one on the field, to see **all six base stats, both types, height in m/ft, weight in kg/lb, baseball ratings, and all ten position fits**. Search also works in the solo round's eligible pool.
- Solo **162–0 Challenge** uses seeded region/type rolling, ten selections, **one region reroll and one type reroll**, and a maximum of three legendary/mythical Pokémon. The separate **151 Challenge** offers a random Kanto Pokémon each round and five Pokémon rerolls. Both challenges save progress locally.

## Position fit formulas (0–100)

**All eight inputs are used in every single position.** The table shows each input's relative impact. Each row totals exactly 100%. `SpA` means Special Attack and `SpD` means Special Defense.

| Pos. | HP | Atk | Def | SpA | SpD | Speed | Height | Weight | Type bonuses (maximum only) | Team importance |
|:--|--:|--:|--:|--:|--:|--:|--:|--:|:--|--:|
| C | 15% | 10% | 25% | 9% | 20% | 7% | 6% | 8% | normal +2, poison +3, rock +3, steel +3, psychic +1, dragon +1, fairy +3 | 9.5% |
| 1B | 14% | 30% | 10% | 18% | 7% | 5% | 8% | 8% | normal +3, fighting +1, ground +3, rock +2, steel +1, grass +3, ice +2 | 8.5% |
| 2B | 6% | 8% | 25% | 13% | 17% | 24% | 4% | 3% | poison +1, bug +3, ghost +1, water +3, electric +3, psychic +3, fairy +2 | 8.5% |
| 3B | 10% | 24% | 25% | 13% | 6% | 11% | 6% | 5% | fighting +1, poison +2, ground +1, rock +2, steel +2, fire +2, dark +2 | 8.5% |
| SS | 4% | 7% | 28% | 11% | 17% | 27% | 4% | 2% | flying +2, ground +2, bug +2, ghost +3, electric +1, psychic +2, dark +3 | 11% |
| LF | 7% | 24% | 15% | 19% | 9% | 19% | 4% | 3% | normal +2, flying +1, poison +3, bug +1, ghost +2, fire +2, grass +1, dark +2 | 7.5% |
| CF | 5% | 10% | 24% | 11% | 12% | 30% | 5% | 3% | flying +3, bug +2, water +1, electric +2, ice +3, dragon +3, fairy +2 | 10% |
| RF | 8% | 24% | 22% | 14% | 7% | 17% | 5% | 3% | flying +2, rock +2, steel +3, fire +2, water +3, grass +2, ice +1, dragon +1 | 7.5% |
| DH | 13% | 34% | 3% | 29% | 6% | 3% | 5% | 7% | normal +2, fighting +2, fire +3, grass +3, dragon +3, dark +1, fairy +1 | 9% |
| P | 16% | 21% | 7% | 22% | 19% | 4% | 7% | 4% | fighting +2, ground +1, ghost +1, water +1, electric +1, psychic +1, ice +1 | 20% |


### Rebalanced type bonuses (v2.1)

- **No single type is a universal favorite:** all 18 Pokémon types receive an advantage at exactly **four of the ten positions**. Every role has seven or eight favored types.
- **Bonuses are small:** the maximum type bonus is now **+3 fit points**, reduced from +6. Dual-type Pokémon receive only their largest applicable bonus, never both.
- **Comparable overall opportunities:** multiply each type's bonus at each role by that role's importance in Team Score; totals range from **0.740 to 0.780** across the 18 types. Flying's corresponding total is **0.745**, so it is no longer uniquely advantaged across the outfield.
- Your six base stats, height and weight still determine the overwhelming majority of each 0–100 position fit; the team formula remains unchanged.
- **`POSITION_WEIGHTS.csv`** includes the exact per-position bonuses; **`TYPE_BALANCE.csv`** provides an at-a-glance comparison of all 18 types. The game engine and in-game Scouting Report use the same figures.

**How the eight values are normalized** to 0–1: HP from 20–150; Attack, Defense, SpA, SpD from 20–140; Speed from 20–130; height in meters from 0.3–2.2; and weight by a logarithmic scale from 5–400 kg. Inputs below or above these ranges are capped. For example, 70 Attack contributes `((70-20)/(140-20)) × 21 = 8.75` points to pitcher fit. The fit is the sum of the eight weighted contributions (out of 100) plus **the single largest** eligible type bonus (never stack two type bonuses), clamped to 100.

**Overall Team Score (0–1000):** 84% of the position-importance-weighted average of the ten fits, plus 16% of the average of the two worst fits, produce a `raw` score. The game then converts it to `round(clamp((raw - 14) / 78 × 1000, 0, 1000))`. Pitcher contributes 20% of the weighted fit. This is our original baseball design and can be changed in `engine.js`; it is not the original creator's football score calibration.

The website includes a collapsible **Scouting report** listing every formula and all type bonuses. A full scouting popup is displayed when you hover or focus any Pokémon.

## Baseball simulation

### Solo challenge

- A regular season contains **162 games** against increasingly strong Pokémon-themed opponents (alternating home/away).
- The entire year is simulated quickly on your computer and saved to browser storage.
- Nine-inning games (extra innings on ties), baseball scores, a line of innings for each game, hits, win/loss record and individual batting/pitching totals.
- You can reposition Pokémon and simulate again; different arrangements change player fit and the simulated results.

### Multiplayer leagues

- **Each team plays exactly 162 games**, regardless of whether the league has 3, 4, 5 or 6 teams. The schedule repeats balanced matchups; extra opponents are added to a circular matchup so no team gets fewer than 162.
- An inning-by-inning at-bat simulator produces strikeouts, walks, singles, doubles, triples, home runs, stolen bases, and scoring. The pitcher and batters use their Pokémon-derived abilities and position fit scores, so the games depend on your draft.
- Regular-season W/L, run differential, complete scoreboard, and totals for all teams. Top two teams compete in a **best-of-seven championship** (first to four wins).
- Position-player stats include PA, AB, hits, AVG, OBP, SLG, HR, RBI, runs, BB, SO, and SB. The pitcher's line includes IP, ERA, WHIP, strikeouts, walks, hits, HR allowed, earned runs.
- Each team uses its single drafted pitcher for the whole simulated season because this is a **10-Pokémon no-bench mode**, not a real-world 26-player roster. Batting order is automatically set from Pokémon hitting/speed ratings. This is a game system, NOT a realistic MLB statistics predictor.

## Running the automated test

Run `node smoke-test.js` in the project folder. It creates a league with three independent human-seat tokens, executes a thirty-pick snake draft, tests chat and a lineup swap, runs a 162-game-per-team season and checks the post-season.

## Hosting a shared site

The supplied game has a built-in Node HTTP server, so it needs a host that runs `npm start`. It **cannot** be deployed as static HTML alone or unchanged to purely serverless hosting.

For a quick experimental share link: push files to GitHub, create a Web Service on Render (https://render.com/), choose Node, run `npm start`, and send the public Render URL with your game invitation codes. Verify Render's *current* free tier limits and sleeping policy before using it. **Important:** saved league state currently lives in `data/leagues.json` on the server's local disk. On an ephemeral free host it may disappear after shutdown/redeploy. Use PostgreSQL/Supabase or other persistent storage before hosting important long-lived leagues. Gitignore excludes this live data by default.

## Source files and limitations

`data/` contains reference copies of your original `fantasy.js`, `draft.js`, `field.js`, `pokemon.js`, `pokedata.js` and the newest supplied `app.js` (saved as `original_app.js`). The working baseball implementation is in `engine.js`, `server.js`, and `public/`.

Most **gameplay** mechanics have been ported or adapted, but we do **not** claim to have faithfully copied every All-22 feature. The original browser files call server modules not supplied (`api/fantasy.mjs`, `api/_lib/fantasy.mjs`, account/profile features, some shiny collections and broadcast resources). Original sign-in, collections, custom Pokémon sprite packs, leaderboard service, and exact television-style broadcast animations are not implemented. The shared-game server and baseball-specific features here were newly coded to replace those dependencies.

This is a personal fan project. Permission to adapt code does not by itself give trademark or artwork rights for Pokémon. Sprites are loaded from PokéAPI's public sprite repository and may require an internet connection; Pokémon characters/artwork remain protected intellectual property.
