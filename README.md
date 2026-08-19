# Go Battle Mode

A single-file team preview calculator for Pokémon GO's Great League. Type what your opponent shows you, get every matchup at every shield count before the timer runs out.

Built by **aayush1621**.

---

## What it does

You get ~15 seconds in team preview and two of your opponent's three Pokémon. This turns that into a decision.

Type a few letters per Pokémon and you get, for each of theirs:

- **A shield matrix** — your result at 0–0, 1–1 and 2–2 shields, with HP remaining on both sides
- **CMP** — whether you win charged-move priority ties (higher Attack resolves first)
- **Their full move pool** — fast and charged, with move counts and damage against each of your three
- **Your damage** back against them
- **A read** summarising which of your Pokémon holds up and which of theirs is the problem

Everything is simulated live. Swap any move and the whole board recalculates.

## Running it

Download `go-battle-mode.html` and open it. That's the whole install.

- **Laptop** — double-click. Any modern browser.
- **iPhone** — save to Files, open, then Share → *Add to Home Screen* for a fullscreen launcher.
- **Android** — open in Chrome, menu → *Add to Home screen*.

No server, no build step, no dependencies. Works offline (see [Offline](#offline) below).

## Searching

Type a few letters and it autocompletes. **Space** accepts and moves to the next slot, so a full team is about nine keystrokes.

Results are ordered by meta ranking, not alphabetically — `fer` gives Feraligatr, not Ferroseed. `licki` gives Lickilicky, not Lickitung. Typing a full name still wins, so `lickitung` gets you Lickitung.

### Forms

A dot marks the modifiers. It works before the name, after it, or as its own word — whichever suits how fast you're typing:

```
marowak          Marowak
marowak.         Shadow Marowak
.marowak         Shadow Marowak
marowak.a        Alolan Marowak
a.marowak        Alolan Marowak
marowak .a       Alolan Marowak      ← attaches to the previous word
marowak.as       Shadow Alolan Marowak
```

| Modifier | Form |
|---|---|
| *(bare dot)* | shadow |
| `s` | shadow |
| `a` | alolan |
| `g` | galarian |
| `h` | hisuian |
| `p` | paldean |

Or ignore the syntax entirely — every form of whatever you typed appears as a tappable chip under the input, and each opponent column has a form switcher in its header.

## Reading the output

**Everything is from your side.** Green means *you* win.

Matrix cells read `your HP · their HP` at the end of the fight:

```
2s   22·0     you win with 22% left
1s    0·23    you lose, they finish on 23%
0s    1·0     you win by a sliver
```

Scan a column top to bottom. `W/W/W` or `L/L/L` means shields don't change anything. A column that flips is where your shield decision actually decides the game — those are the ones worth memorising.

The **cmp** bar is green when your Attack is higher, meaning you win simultaneous charged-move exchanges. Red means throw a turn earlier rather than trusting the race.

### Damage colours

Damage is shown as a percentage of the target's full HP:

| | |
|---|---|
| under 25% | grey — chip |
| 25–49% | pale — meaningful |
| 50–74% | amber — over half their bar |
| 75–99% | orange — nearly lethal |
| 100%+ | red — kills outright |

**Red always means lethal**, whichever list you're reading. In their move list that's your Pokémon dying; in *your damage* it's theirs.

### Fast move numbers

`4.0 ept` and `3.0 dpt` are **per turn** — the only fair way to compare a 2-turn move against a 1-turn one. Shadow Claw gains 8 energy over 2 turns; Dragon Breath gains 4 over 1. Both are 4.0 EPT, identical generation.

The count circles on charged moves use energy **per use**, since that's what determines how many fast moves you need. Feraligatr with Shadow Claw reaches Hydro Cannon in 5. A `⁻` means you overflow energy rather than landing exactly.

## What's included

- All **1144** Great League–eligible Pokémon, including 367 shadow and 57 regional forms
- Every available fast and charged move per Pokémon, with elite/legacy moves tagged
- Optimal 1500 CP spread solved per Pokémon (shadows get the 1.2× / 0.833× multipliers)
- 777 sprites packed into one embedded atlas

## Offline

Everything — data, simulator, sprites — is embedded in the HTML. The only network request is two Google Fonts. With no connection it falls back to system fonts and loses nothing functional.

Saved teams use `localStorage`, so they persist per browser.

## Limitations

Worth being clear about these.

**It doesn't predict the match.** It simulates 1v1s. A 3v3 is decided by lead choice, switch timing, energy carried between switches, and whether your opponent shields correctly — none of which a 1v1 sim can know. The read panel counts matchups; it deliberately stops short of calling a winner.

**Bait logic is simple.** It throws the cheaper charged move when the opponent has shields. Real players bait better and worse than that, so exact HP percentages drift from PvPoke's simulator. Trust the shape — *which shield count flips a matchup* — over the exact numbers.

**Backline inference is type logic, not data.** The note about their likely third Pokémon is derived from what their revealed pair is weak to. It is not crowd-sourced from real battles, and it's labelled as such in the app.

**The data is a snapshot.** When PvPoke updates for a new season or a move rebalance, this file goes stale silently. Rebuild it.

## Data sources

All data is baked in at build time from public sources:

| Source | Used for |
|---|---|
| [pvpoke/pvpoke](https://github.com/pvpoke/pvpoke) `src/data/rankings/all/overall/rankings-1500.json` | rankings, recommended movesets |
| [pvpoke/pvpoke](https://github.com/pvpoke/pvpoke) `src/data/gamemaster.min.json` | base stats, types, move power/energy/turns |
| [PokeAPI/sprites](https://github.com/PokeAPI/sprites) | sprite artwork |

Huge credit to **PvPoke** — the rankings and game data that make this possible are theirs. Check their repository for licence terms before redistributing anything derived from it.

Pokémon and Pokémon GO are trademarks of Nintendo / Creatures Inc. / GAME FREAK inc. and Niantic. This is an unofficial fan tool with no affiliation.

## Rebuilding for a new season

1. Pull fresh `rankings-1500.json` and `gamemaster.min.json` from the PvPoke repo
2. Recompute the 1500 CP spreads (max stat product under the cap)
3. Repack the sprite atlas if the roster changed
4. Re-embed and ship

## Licence

Your call — but note that the bundled PvPoke data carries its own terms, so check theirs before picking one.

---

Built by [aayush1621]((https://github.com/Aayush-Prranav)).
