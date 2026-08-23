# World Cup of Flags

A 64-flag sumo tournament in a single HTML file. No build step, no dependencies.

Two flags kick off on a football pitch, each defending one end. Every impact adds
damage, and the more damage a flag carries the further the next hit throws it — so
matches escalate until somebody is bundled into the goal they were defending.
Touchlines and the woodwork bounce you back; the goal mouth is the only way out.
Win six rounds to lift the cup.

## Run it

Open `index.html` in a browser, or serve the folder:

```bash
python3 -m http.server 8080
```

## Playing

Pick **PLAY** and choose a flag, and you control it whenever it's your match — the rest
of the bracket is played by the AI. Pick **WATCH** to spectate the whole tournament.
You can switch between them mid-cup with the WATCH/PLAY button.

| Control | Action |
| --- | --- |
| Drag on the arena | Steer toward the pointer |
| `W A S D` / arrow keys | Steer |
| `SPACE` or **SLAM** | Charge a dash — big knockback, costs stamina |
| `ENTER` | Fight / pause / next match |
| **AUTO** | Play the bracket through without prompting |
| **SIM REST** | Instantly resolve every remaining match |
| 🔊 / 🎙 (header) | Toggle sound and spoken commentary |

The pitch resizes itself to whatever the device leaves free, so the controls stay on
screen. Turn a phone sideways and the layout splits in two — pitch on the left, score
and controls on the right — instead of making you scroll during a match.

The ring around each flag shows its damage. Green is safe, red means the next clean
hit will probably put it in the net. The goal you must not be pushed into is marked
`own net` and glows in your colour. After 8 seconds sudden death starts stretching
both goal mouths toward the full width of the goal line.

Leaving your flag idle is not a loss — an unattended flag falls back to AI steering,
and any input from you takes over instantly.

## Sound

All audio is synthesised at runtime — there are no asset files. Web Audio builds a
stadium crowd bed (looped brown noise that swells on heavy hits and erupts on a goal)
layered under an electronic loop that gains arpeggios as the tension and the round
climb, plus a referee whistle, impact thuds scaled by impact power, dash whooshes, a
sudden-death siren, a goal horn and a closing fanfare.

Commentary uses the Web Speech API. Lines are prioritised: goals and the final cut
straight in, colour commentary is dropped if the commentator is already mid-sentence
or spoke within the last 2.6 seconds. The music and crowd duck automatically while a
line is being read. Roughly 1.6 lines per match over a full tournament.

Both toggles live in the header and are remembered in `localStorage`. Browsers block
audio until you interact with the page, so it starts on your first click.

## Tests

The simulation core in `index.html` is fenced between `==SIM-CORE-START==` and
`==SIM-CORE-END==` markers and is deliberately free of DOM access, so it can be
verified headlessly:

```bash
node tests/headless.mjs
```

It checks the roster, runs 500 randomised matches and 200 full tournaments, and
asserts that matches terminate, that they're decided by an own-net goal rather than
the clock, that the loser always ends up in the net it was defending, that nobody
escapes through a touchline, and that no flag is ever lost or duplicated by the
bracket.
