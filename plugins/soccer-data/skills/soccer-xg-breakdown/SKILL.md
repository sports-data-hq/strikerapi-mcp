---
name: soccer-xg-breakdown
description: "Use when the user asks whether a soccer result, club or player matches the chances created, in fan or analyst wording: 'were Spurs unlucky', 'did they deserve the win', 'who had the better chances', 'xG race for the derby', 'is Salah overperforming', 'big chances missed', 'shots against Liverpool', 'xG story of last night'. Use this whenever results are compared with shot quality. Load it for short fragments and follow-ups too, even with no name given, instead of answering from memory or going straight to the raw tools. Shows shots with location, outcome and our xG, big chances, and finishing over or under expected with sample size. Not for pre-match previews (see soccer-match-preview), a club's style (see soccer-team-style-profile), title odds (see soccer-season-simulation), hockey expected goals (see nhl-xg-report) or building an xG model."
metadata:
  version: 2.0.0
  author: SportsDataHQ
---

# Soccer xG breakdown

Expected goals tells you who made the better chances, not who won. This skill turns StrikerAPI's located shots into the story of a match, or a club's or player's finishing over a season.

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server. Our xG is a location model: the league's goal rate for shots from the same distance and angle, header or not; penalties at the league's conversion rate. It does not see defenders or the pass. Say so once.
- Located shots exist from 2012-13 in the big leagues (partial that season for some); check coverage before a long history.
- Pitch frame: x is distance from the goal line as a share of half the pitch (penalty spot about 0.23), y across (0.5 = centre).

## One match (about 17 credits)
1. **Find the match.** `list_games(team, date)` (2 cr) for the game id.
2. **The match file.** `get_game(game_id)` (5 cr): score, both clubs' xG, shots, possession, key events.
3. **Every shot.** `get_events(game_id, kind: "shots")` (10 cr): minute, shooter, location, body part, outcome, xG.
4. **Build the story:** the xG race by minute (cumulative), the 3 biggest chances (highest xG) and what happened, penalties shown separately (non-penalty xG), and game state (who was leading when) because trailing teams shoot more.

## A club or player over a season (about 10-20 credits)
1. `get_shot_map(team or player, season)` (10 cr): totals, zones, xG, goals; add `against: true` for shots conceded.
2. Finishing vs expected = goals - xG, with shots and seasons as the sample. One season of over-performance is mostly noise for most players; say how many shots it rests on.

## Output template (one match)
```
<HOME> <g>-<g> <AWAY> | xG <x.xx>-<x.xx> (non-penalty <x.xx>-<x.xx>) | shots <n>-<n>
xG race: <who led when, key swings by minute>
Best chances: <minute, player, xG, outcome> x3
Verdict: <who created more and better, with the numbers>
Data: list_games, get_game, get_events | <league>, season <yyyy>
```

## Rules
- Always say "our xG" and the location-only caveat once.
- Separate penalties; report shot counts beside xG; flag tiny samples (under 30 shots for a player).
- No betting language.
- Out of credits: if a call is refused for credits, pass on the API's message as it comes (it says when free credits refill and how to add more), and stop; don't retry or switch tools to get around it.