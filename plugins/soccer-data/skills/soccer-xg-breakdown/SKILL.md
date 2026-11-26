---
name: soccer-xg-breakdown
description: "Use when the user asks whether a soccer result, club or player matches the chances created, in fan or analyst wording: 'were Spurs unlucky', 'did they deserve the win', 'who had the better chances', 'xG race for the derby', 'is Salah overperforming', 'big chances missed', 'shots against Liverpool', 'xG story of last night'. Use this whenever results are compared with shot quality. Load it for short fragments and follow-ups too, even with no name given, instead of answering from memory or going straight to the raw tools. Shows shots with location, outcome and our xG, big chances, and finishing over or under expected with sample size. Not for pre-match previews (see soccer-match-preview), a club's style (see soccer-team-style-profile), title odds (see soccer-season-simulation), hockey expected goals (see nhl-xg-report) or building an xG model."
metadata:
  version: 2.2.0
  author: SportsDataHQ
---

# Soccer xG breakdown

Expected goals tells you who made the better chances, not who won. This skill turns StrikerAPI's located shots into the story of a match, or a club's or player's finishing over a season, and says how much of the gap between results and chances is noise.

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server. Our xG is a location model: the league's goal rate for shots from the same distance and angle, header or not; penalties at the league's conversion rate. It does not see defenders, the pass or the keeper. Say so once.
- Seasons are named by the year they start (2026 = 2026-27). MLS, NWSL, Liga MX, Brazil and Argentina are calendar years. Every tool takes `league` (soccer = Premier League by default; mls, laliga, seriea, bundesliga, ligue1, ucl, championship and others).
- Located shots exist from 2012-13 in the big leagues (partial that season for some); older matches carry goals, cards and substitutions only. Check coverage before a long history.
- Pitch frame: x is distance from the goal line as a share of half the pitch (penalty spot about 0.23), y across (0.5 = centre). Shots also carry `zone`, `body_part`, `penalty` and `distance_m`.

## One match (about 17 credits)
1. **Find the match.** `list_games(team, date)` or `list_games(from, to, team)` (2 cr) for the `game_id`. Use `status: final`; for a match not yet played, hand off to soccer-match-preview.
2. **The match file.** `get_game(game_id)` (5 cr): score, both clubs' xG, shots, shots on target, blocked shots, possession, `penalty_goals` and `penalty_attempts`, key events.
3. **Every shot.** `get_events(game_id, kind: "shots")` (10 cr): minute, shooter, assister, location, zone, body part, outcome, xG, penalty flag. Check `count` and `next_offset`; a match has about 20 to 35 shots.
4. **Who was missing (optional).** `get_availability(team, matches: 10, league)` (5 cr per club) when the user asks who was out, or a surprising lineup or finishing gap needs context: each squad player's status and recent matchday squads. It reflects the latest squads, not the match date; for a past match use `get_lineups(game_id)` (5 cr) to see who actually started and played. The tool cannot separate injury, suspension and selection; say "absent from the squad" and never name a reason.
5. **Optional, state and sequence.** `get_events(game_id, kind: "cards")` (10 cr) when a red card changes the match.

## A club or player over a season (about 10 to 20 credits)
1. **Finishing and shot quality.** `get_shot_map(player or team, season, include_shots: false)` (10 cr): totals, zones with goals and xG, `xg_per_shot`, conversion. Add `against: true` with a team for shots conceded.
2. **Context.** `get_player(player_id)` (5 cr) for minutes and per-90 rates, or `get_team_stats(team, season)` (5 cr) for the club's xG for and against per match against the league.
3. **Who is overperforming.** `get_leaders(stat: "xg", season, per90, min_games)` and `stat: "goals"` (5 cr each) to line a player up beside the league.
4. **Several seasons.** One `get_shot_map` per season (10 cr each) so the gap is read season by season, never pooled blindly.

## Method
- **Non-penalty xG** = total xG minus the xG of shots with `penalty: true`. Quote it first; report penalties separately. A penalty is worth about 0.76 xG and one decides many "unlucky" stories.
- **xG per shot** = xG / shots. Volume vs quality: many shots at 0.05 each is a different story from five shots at 0.3.
- **Big chance** (our label, not a provider tag) = a shot with xG of 0.30 or more. Count them, then count how many were scored; "big chances missed" is the difference.
- **xG race:** running total of xG by minute for each side with goals marked. Group shots in the same minute from the same side into a sequence (a block, save or post followed by a rebound): the sum counts every shot, so one attack reads as the total of its shots. Show the match xG both ways: as summed, and with each sequence counted at its largest shot as a floor.
- **Game state:** score before each shot (0-0, leading, trailing). Trailing sides shoot more and from worse spots; leading sides sit. If the winner was ahead from the 33rd minute, its lower xG in the rest of the game is expected, not a flaw.
- **Spread of luck:** variance of a match's goals from its shots is the sum of xG x (1 - xG) over all shots; the standard deviation of the xG difference is the square root of that over both sides, usually 1.3 to 1.6. If the xG difference is under 0.5, say the chances were level.
- **Finishing vs expected** = non-penalty goals minus non-penalty xG, over a stated number of shots. Standard deviation is at most the square root of xG x (1 - xG / shots); z = (goals - xG) / that. Under 2 means inside the range of luck.
- **Per-90 and per-shot:** goals per shot against xG per shot, so a finisher of 0.20 per shot on 0.17 xG per shot is shown with both.
- **Where the shots come from:** share of xG in the six-yard box, penalty area and outside the box; header share. A club living on long shots has lower-quality chances than its shot count suggests.

## Output template (one match)
```
<HOME> <g>-<g> <AWAY> | xG <x.xx>-<x.xx> (non-penalty <x.xx>-<x.xx>) | shots <n>-<n> (on target <n>-<n>) | xG per shot <x.xx>-<x.xx>
xG race: <who led when, key swings by minute, sequences flagged>
Best chances: <minute, player, xG, outcome> x3 | big chances (xG 0.30+): <n scored of n> each side
Game state: <minutes leading / level / trailing and what it did to shot volume>
Verdict: <who created more and better, the xG gap against the +/- 1.4 spread, and what the result says about luck>
Missing (if asked): <players absent from the squad, reason not in the data>
Data: list_games, get_game, get_events, get_availability | <league>, season <yyyy>
```

## Output template (club or player season)
```
<Name> | <league>, <season> | <shots> shots, <minutes> minutes
Goals <g> vs non-penalty xG <x.xx> (penalties <n>) | xG per shot <x.xx> | conversion <x%>
Finishing gap: <+/-x.x> goals, z <x.x>: <inside / outside the range of luck>
Where the shots come from: <zones with shots, xG, goals>
Prior seasons: <gap each season with shots>  | Data: get_shot_map, get_player, get_leaders | <season>
```

## Worked example (live calls, Nov 25, 2026)
Newcastle United 2-2 Arsenal, Nov 21, 2026, Premier League (`get_game` for the totals, `get_events` shots for every shot, 25 shots).
- xG 2.12 to 1.64, no penalties so non-penalty xG is the same. Shots 12 to 13, xG per shot 0.18 to 0.13. Variance of the shot outcomes gives a standard deviation of about 1.46, so the 0.48 gap is a third of one standard deviation: level on chances, a fair draw.
- Race: Arsenal led from Eze's 33rd-minute goal (0.22 xG) and had the 28th-minute miss from the six-yard box by Bruno Guimaraes (0.48, the biggest Arsenal chance). Newcastle equalised at 49 (Barnes, 0.21), then in the 62nd minute came three shots in one attack: Burn's blocked header (0.13), Willock's miss from the six-yard box (0.88) and Thiaw's goal (0.61), 1.62 of Newcastle's 2.12. Counted at its largest shot that attack is 0.88, which takes Newcastle to about 1.4 against Arsenal's 1.64. Madueke's 78th-minute goal (0.17) levelled it.
- Big chances (xG 0.30+): Newcastle 2 (one scored), Arsenal 1 (missed).
- Verdict wording: "Newcastle created 2.12 to 1.64 as summed, 1.4 to 1.64 with the 62nd-minute scramble counted once, and either way the gap is inside the match's roughly 1.5-goal spread. Chances were level; a draw was fair."
Season check on the same data: Erling Haaland, 2026 Premier League, 9 goals from 7.32 xG on 42 shots (no penalties, xG per shot 0.17). Gap +1.7, standard deviation at most 2.5, z at most 0.7: inside the range of luck after 11 matches.

## Not enough data
- Under 30 shots for a player or 100 for a club: finishing gaps are noise; say so and do not call it a trend.
- One season: a gap in one season is mostly luck; require the same sign in two seasons with 100+ shots each before calling it a skill, and still say "suggests".
- Matches before 2012-13, or a league without located shots: no xG; give the score and the box-score shots only.
- Tool returned fewer shots than the box score: say the shot list is incomplete and use `get_game` totals.
- Partial xG seasons (Premier League and Liga MX in 2012-13): label the season partial.
- Possession can be missing for some leagues and seasons; do not infer it.

## When there's no match

If the match the user names has not been played, don't stop to ask. Do the most useful real version and say which in the first line: the club's last completed match with the same breakdown, or the club's season xG for and against from `get_team_stats` and a pointer to soccer-match-preview for the fixture. If the league is between seasons, use the last completed season and say so.

## Rules
- Who was missing: report the squad status the tool gives, with last appearance; never say injured or suspended, because the data cannot tell.
- Always say "our xG" and the location-only caveat once.
- Separate penalties; report shot counts beside xG; quote the standard deviation or the sample next to any luck claim.
- Every number names its tool (the Data line, plus inline where it could be doubted).
- No betting language: no picks, stakes, leans or "value". If the user asks what the market thought, use `get_game_probabilities(game_id)` (5 cr) for the no-margin home, draw and away numbers as information, and never name a book.
- Sequences (last 5, recent form, a run of results) are quoted in the order the tool returns them, with dates; never retell them from memory. Lists in the answer must match the tool rows.
- Rate limit: a "rate limit exceeded" reply is not a credit refusal. Wait a few seconds and retry that call once, one call at a time; if it still fails, name the section that is missing.
- Out of credits: if a call is refused for credits, pass on the API's message as it comes (it says when free credits refill and how to add more), and stop; don't retry or switch tools to get around it.
