# Supply Needs Planner

Right-click a station (or its build storage) with selected trader ships and have
them queue **Execute Trade** deals that fulfil that target's needs — buying the
wares from your own stations (or any non-hostile station) and delivering them.

The orders are ordinary **Execute Trade** deals — the same kind a station trader
makes. What counts as the target's "needs" is simply the buy offers the target
station (or its build storage) is already advertising.

## Why

Sometimes a freshly planned station just sits there waiting — in a forgotten
corner of the universe or right amid the densest traffic lanes — while dozens of
trader ships stream past hauling wares from A to B, never to it. Most stations
do get built eventually (some trader decides to visit), but it is not a rare
situation either: a station can wait a very long time for even the first
module's wares.

And until that first module stands, the game will not let you assign a manager.
No manager means no subordinates, which means no `Trade for Build Storage`
ships. From there your options are: 1 - wait, 2 - forget, 3 - start frantically
clicking your own trader ships through manual trades — first removing whatever
orders they already had (harmful in itself), then looping "buy ware - set
quantity - confirm, sell ware - set quantity - confirm", six clicks per order.

This mod is option 4: one right-click on the **station itself** (it resolves the
build storage for you, so it works before the first module is up), one config
window, one **Run planner**. The screenshot run queued 100 trade orders for 5
ships — 600 clicks saved.

Use cases:

- Kickstart a freshly planned station's build.
- Fill a station's build storage completely.
- Top up a shipyard with a targeted caravan run.

## Game version compatibility

- 9.00 release - **supported**
- 9.00 betas and release candidates - **supported**
- 8.00 release - **supported**

## Dependencies

- **SirNukes Mod Support APIs** ([link](https://www.nexusmods.com/x4foundations/mods/503)) — hard
  dependency. Provides the Lua loader, Interact Menu API and Simple Menu API.

## Usage

1. Select one or more cargo/trader ships.
2. Right-click any non-hostile station (your own, or friendly/neutral NPC).
3. Choose **Fulfill station needs** or **Fulfill build storage needs**
   (the build-storage entry only appears when the station has a build storage).
4. Tune the config and click **Run planner**.

## How it plans

Clicking **Run planner** plans every round trip in one pass and writes all the
resulting buy/sell orders straight into the ships' queues. Each ship gets one
or more round trips: a trip loads from source stations up to the
*round-trip fill target*, then delivers the lot to the target in a
single drop. When a trip finishes the ship rolls into the next queued one, and
keeps going until the target's needs are covered (or you cancel the orders). The
selected ships share the work, so the same stock is never booked twice.

Each trip is **spread across wares** rather than filling up on one. A station is
built module by module and every module needs a little of each resource, so a
trip first gives every needed ware a fair slice (up to *Max wares per trip*),
then tops the cargo up toward the fill target. The result is that the target
starts receiving a bit of everything from the very first trip instead of waiting
several trips for the last ware to show up. When there are more needed wares than
*Max wares per trip*, the wares that lead each trip rotate so none is starved —
raising *Max wares per trip* and *Max trades per trip* lets a single trip carry
more variety. Multiple selected ships divide the wares between them, so the first
round of trips fans out across distinct wares: e.g. 3 ships with *Max wares per
trip* = 2 can cover 6 different needs in the very first round.

Within a trip the ship buys **farthest source first** and the source nearest the
target last, so the loaded final hop to the delivery point is as short as
possible.

## Config

| Setting | Meaning |
| --- | --- |
| Source restriction | `Only own stations`, `Own stations first`, or `No restrictions` (any known, non-hostile station). |
| Source priority | Pick the `Cheapest` source first, or the `Nearest` to the target. |
| Max source distance | Ignore sources more than this many gate jumps from the target. |
| Max price above average | Skip a source priced more than this % over the ware's average price (500 = effectively no cap). |
| Fulfill percentage | Cap each need at this share of the target's current demand. |
| Max wares per trip | Distinct wares a ship loads on one round trip. |
| Max trades per trip | Total source pickups (buy legs) on one round trip. |
| Max round trips per ship | Cap how many round trips a single ship is queued. |
| Round-trip fill target | Close a trip once it reaches this % of cargo (0 = fill to capacity). |
| Minimum cargo usage per trip | Discard a trip (and stop the ship) below this % full — keeps ships from making near-empty runs. **Setting this too high can leave small remainders unfulfilled.** |
| Minimum per-trade fill | Reject any single buy leg smaller than this % of cargo — avoids trivial pickups. **Setting this too high can skip valid small trades.** |

**Restore default config** / **Use previous config** are available in the menu;
"Use previous" is greyed out until a run has saved one.

### Tuning note: priority, sources and the cargo floor

These settings interact. In X4 a station's price for a ware rises as its stock
falls, so the **cheapest** sources are usually the ones holding the **most**
stock — which also means bigger pickups per stop. Your **own** stations tend to
sit on large stockpiles too. **Nearest** sources, especially under **No
restrictions**, are often small nearby NPC factories: pricier, and only a few
units each, so a trip has to make many small pickups to fill up.

The practical consequence: a high **Minimum cargo usage** is hard to reach with
`No restrictions` + `Nearest`, because the small fragmented pickups run into the
**Max trades per trip** limit before the ship is full, and the under-filled trip
is discarded — so some wares (the ones with only tiny nearby sellers) may never
get delivered. In testing, floors much above ~40% started leaving wares unfilled
on that combination.

To soften this, **Nearest** already self-corrects: whenever a nearest-sourced
trip can't reach the cargo floor, it retries that same basket once with the
sources re-ordered by biggest stock first — larger legs from deeper stockpiles,
which usually clears the floor at the cost of a slightly longer route. It's a
rescue, not a guarantee, so if you still see the "hit the Max trades limit"
notice or wares going undelivered: lower **Minimum cargo usage**, raise **Max
trades per trip**, or switch priority to **Cheapest** (and/or **Own stations
first**) to pull from bigger, deeper stockpiles from the start.

## Credits

- Built on **SirNukes Mod Support APIs**.
- By VasiliyTemniy.

## Source

https://github.com/VasiliyTemniy/x4-foundations-supply-needs-planner
