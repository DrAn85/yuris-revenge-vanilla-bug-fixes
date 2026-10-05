# Yuri's Revenge 1.001 bug-fix patch v1.0

## What it is

A bug-fix patch for `gamemd.exe` of *Command & Conquer: Red Alert 2 - Yuri's Revenge*, version 1.001.
This is version **v1.0**.

- Fixes **362** bugs of the original game: 354 are always on, 6 are switches (off by
  default, see "`[Fixes]` switches" below), 1 is partly always on and partly a switch, and 1 is
  covered by another fix. Every one is listed in "Full list of fixes" at the end of this file.
- Bug fixes only: no balance changes, no new units, features or ini settings (apart from the 7 `[Fixes]`
  switches). Fixes that change the original's default behaviour, or whose status as a bug is debatable, are
  switches that are off by default.
- The patcher `yrfix_patch.exe` contains only the bytes that differ from the original, not the game program; you need
  an unmodified original `gamemd.exe` to use it.

| File | Size | MD5 |
|---|---|---|
| original `gamemd.exe` (Yuri's Revenge 1.001) | 5,286,208 bytes | `2a1307f6f57c9c492f985b4e3afb88e6` |
| patched `gamemd.exe` (v1.0) | 5,722,112 bytes | `23e6d26b92e732bc5b2dc1762d3f1449` |
| `yrfix_patch.exe` (v1.0) | 606,208 bytes | `1d56f9788feca0e9090ef5e4c834f3f8` |

SHA-256 of `yrfix_patch.exe`: `ded99880b050979da542fdabdfd7416eaf0adf943dab4cc46ca71a6ea4f90a02`

## How to use it

**Requirement**: the unmodified Yuri's Revenge **1.001** `gamemd.exe` (5,286,208 bytes, MD5
`2a1307f6f57c9c492f985b4e3afb88e6`). If Ares / Phobos, the CnCNet client or another patch changed your `gamemd.exe`, put the
original file back first; the patcher refuses any other file and then changes nothing.

1. Put `yrfix_patch.exe` into the game folder (next to `gamemd.exe`) and double-click it, or run it from a command
   prompt with the path:
   ```
   yrfix_patch.exe "D:\Games\Yuri's Revenge\gamemd.exe"
   ```
2. The patcher checks the original's MD5, saves the original as `gamemd.exe.bak`, writes the fixes and checks
   the result's MD5 again.
3. When started by double-clicking, the window waits for a key press before closing.

Other uses:

| Command | Effect |
|---|---|
| `yrfix_patch.exe --check` | only report whether `gamemd.exe` is "original", "patched (this version)" or "unknown" |
| `yrfix_patch.exe --revert` | restore the original from `gamemd.exe.bak` (the backup is kept) |
| `yrfix_patch.exe --yes` | do not wait for a key press at the end (for scripts) |

Exit codes: 0 success (or nothing to do), 2 wrong file (not the original, backup is not the original, ...),
3 read / write error (file is read-only, the game is running, ...).

Notes:
- If `gamemd.exe.bak` already exists, the patcher only continues when it is the original; if it is some other
  file the patcher stops - move or delete it yourself.
- The patcher never overwrites someone else's backup and writes nothing when the file is not the expected one.
- For a newer version of the patch: first restore the original with the old patcher's `--revert` (or copy
  `gamemd.exe.bak` back), then run the new patcher.

## `[Fixes]` switches

A few fixes change the original's default behaviour or are debatable; they are switches, **all off by default** - without this section the game behaves like the original. To turn one on, add a section to `rulesmd.ini` (`yes` = on, `no` = off):

```ini
[Fixes]
CloakStopUnitsUncloakWhileMoving=no
AICloakedUnitsDontScatter=no
ArcingProjectilesAimUphillExactly=no
UpgradesUsePowersUpToLevelAnim=no
AAOnlyWeaponsTargetFallingUnits=no
ExplosionsCollapseCliffs=no
SpawnsSurviveOwnerLimbo=no
```

- `CloakStopUnitsUncloakWhileMoving` (CT1-0393): Units with `CloakStop=yes` uncloak while moving and cloak again when they stop (in the original the setting has no effect on moving units). **Only takes effect together with `AICloakedUnitsDontScatter=yes`**; on its own it does nothing.
- `AICloakedUnitsDontScatter` (CT1-0161): Computer-controlled vehicles no longer move one cell aside after they finish cloaking. The original does this on purpose (to make cloaked AI units harder to find), so it stays the default.
- `ArcingProjectilesAimUphillExactly` (CT1-0071): `Arcing=yes` projectiles (e.g. artillery) firing at targets higher than themselves use the correct ballistic angle instead of always 45 degrees, so they no longer overshoot.
- `UpgradesUsePowersUpToLevelAnim` (CT1-0094): Building upgrades show the power-up animation of their own `PowersUpToLevel` (the original picks the wrong animation slot with more than 3 upgrades or on an empty building). **Set it in rulesmd.ini to cover every building type.**
- `AAOnlyWeaponsTargetFallingUnits` (CT1-0183): Anti-air-only weapons also attack falling units (e.g. paratroopers).
- `ExplosionsCollapseCliffs` (CT1-0270): Explosions of ordinary weapons can collapse destructible cliffs with the `[General] CollapseChance` probability (Tiberian Sun behaviour; in the original only a few weapons can). Uses one extra random number when on, so **all players in a multiplayer game must use the same setting**.
- `SpawnsSurviveOwnerLimbo` (CT1-0407): Carriers, Dreadnoughts and other spawner units no longer destroy their launched spawns when they enter a transport or a building; the spawns are recalled when the owner comes out. Destroying or selling the owner still destroys its spawns.

Rules:
- A map file (`.map` / `.mpr` / `.yrm`) may also contain `[Fixes]`; it applies to that game only, the next game starts from rulesmd.ini again. `[Fixes]` in a campaign mission's companion `<mission>.INI` has no effect; put it in the map file itself.
- Switches are not stored in savegames. Loading a game takes the switches from rulesmd.ini; values set by the map are lost on load, so put switches that must survive loading into rulesmd.ini.
- In multiplayer every player must use the same switches (rulesmd.ini must match anyway).

## Compatibility (please read)

**Stated as it is: what has not been tested is marked as not tested.**

1. **Multiplayer**: patched players can only play with players who have **the same version** of the patch. A
   different `gamemd.exe` means desyncs or no connection. The `[Fixes]` switches must also be the same for all.
2. **Ares, Phobos and every other mod injected with Syringe: not compatible.** They hook functions of the
   original by address, and this patch replaces many of those functions. Use `--revert` to play such mods.
3. **Savegames**:
   - The `[Fixes]` switches are not stored in savegames.
   - Some fixes change what a savegame contains or how it is loaded (ids as in the full list below):
     - CT1-0001: The game options are saved and restored with the savegame. (This changes the savegame format.)
     - CT1-0002: The light goes out when the building is destroyed or sold, also after loading.
     - CT1-0080: The freeze and the other states survive saving and loading.
   - From code analysis, savegames made with the original game **probably cannot be loaded** by the patched
     game, and the other way round. **Loading old savegames has not been tested.** Finish running campaigns
     before patching, or keep `gamemd.exe.bak` to switch back.
4. **CnCNet client / spawner version 5**: not tested.
5. **Wine**: the patcher is a plain Win32 console program and should run under Wine (not tested). The game
   itself is expected to behave under Wine as the original does - the patch does not change how the game talks
   to the system (graphics, sound and network use the same system interfaces) - but this has **not been tested**.
6. The patcher is built for Windows XP to Windows 11 (it only uses system functions XP already has), but it
   has **only been run on Windows 10**; XP / 7 / 11 and Wine are not tested. Paths may contain spaces and
   non-English characters.

## Known limitations

- Every fix has been checked at the code level, but **in-game testing is not complete**. Please report problems
  with the output of `yrfix_patch.exe --check`, the steps to reproduce and, if possible, a savegame or recording.
- Only Yuri's Revenge 1.001 is supported; the Red Alert 2 executable (`game.exe`), other versions and
  executables modified for other languages are not.

## Copyright

Command & Conquer, Red Alert 2 and Yuri's Revenge are trademarks of Electronic Arts; this patch is not
affiliated with EA. The patcher does not contain the original game files, only the bytes needed for the fixes.

The patch is provided as is, without warranty of any kind; you use it at your own risk. The patcher keeps a backup
of your original `gamemd.exe` as `gamemd.exe.bak`, and `--revert` puts it back.

## Full list of fixes

The ids (`CT1-xxxx`) are the patch's own bug numbers. Each row says what the original game does (**Before**), what the patched game does (**After**) and whether the fix is always on or a `[Fixes]` switch. Many of the bugs only show up with modified rules, art or maps; the rows name the ini keys involved.

Marks in front of the id:

- no mark: always on (354)
- ⚙ a `[Fixes]` switch, off by default - without it the game behaves like the original (6)
- ◐ partly always on, partly a `[Fixes]` switch (1)
- ↪ covered: the crash is prevented by another fix (1)

Categories: Crashes & out-of-bounds (45), Multiplayer sync (4), Pathfinding & movement (54), Targeting & combat (56), AI (25), Units & AI (1), Economy & ore (13), Triggers & maps (14), Shroud & fog (1), INI & data (2), Graphics & UI (96), Other (51).

### Crashes & out-of-bounds (45)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0008 | Crashes & out-of-bounds | Units that are already dead but still on the map (sinking ships, crashing aircraft, units playing their death animation) can be killed again by more damage: second explosion, double score. | Such units ignore further damage; they die only once. | always on |
| CT1-0010 | Crashes & out-of-bounds | Passengers inside a building or transport erased by a Chrono weapon (e.g. infantry in a Bio Reactor) are deleted without being properly removed from the game's lists. | Passengers are removed from the game properly when their container is erased. | always on |
| CT1-0041 | Crashes & out-of-bounds | A TerrainType with Foundation=0x0 can crash the game or mark wrong cells when placed. | Such terrain objects are placed and occupy no cell. | always on |
| CT1-0059 | Crashes & out-of-bounds | Amphibious infantry without C4=yes die when paradropped or chronoshifted onto water. | They survive and stand in the water; non-amphibious infantry still die. | always on |
| CT1-0075 | Crashes & out-of-bounds | A map with more than 8 starting points in [Header] corrupts game data and can crash (Internal Error at 00529A14). | At most 8 starting points are read. | always on |
| CT1-0120 | Crashes & out-of-bounds | Units lose experience from their missiles, C4 or shots when they cloak or board a transport before the hit; infantry heading for a building erased by a Chrono weapon can crash the game. | Experience and kills are credited; infantry stop when their target building is erased; removed spawns are handled like dead ones. | always on |
| CT1-0121 | Crashes & out-of-bounds | A long-range sonic wave (IsSonic=yes) can crash the game or draw garbage when it travels far. | The far part of the wave is drawn with the last colour step; no crash. | always on |
| CT1-0171 | Crashes & out-of-bounds | Long paths on big maps can crash the game (Internal Error at 0042A525 / 0042C507 / 0042C554). | No crash: the path search no longer writes past its buffer and reads zone numbers correctly. | always on |
| CT1-0215 | Crashes & out-of-bounds | Paratroopers whose bridge is destroyed while they descend die on landing. | Those landing on a passable cell survive. | always on |
| CT1-0218 | Crashes & out-of-bounds | Paradropped units with Crashable=yes shot down in the air land alive; paradropped NotHuman=yes infantry killed in the air crash to the ground. | Crashable drops crash and die; NotHuman paratroopers explode in the air like human ones. | always on |
| CT1-0220 | Crashes & out-of-bounds | Repairing a unit whose Strength is lower than RepairStep crashes the game (Internal Error at 007120F7). | It is repaired normally. | always on |
| CT1-0222 | Crashes & out-of-bounds | Aircraft and jumpjet units shot down outside the playable map area are never cleaned up and keep blocking build limits. | They are removed at once. | always on |
| CT1-0241 | Crashes & out-of-bounds | A unit type with WalkRate=0 crashes the game the moment it starts moving. | No crash; the walk animation simply does not advance. | always on |
| CT1-0243 | Crashes & out-of-bounds | Ore growth and spread can crash the game after a while (a fully grown field nobody harvests) and stop spreading next to trees. | No crash; spreading continues. | always on |
| CT1-0261 | Crashes & out-of-bounds | A unit frozen by a Chrono Legionnaire that then cloaks stays frozen forever, and killing the Legionnaire crashes the game. | The victim is released when it cloaks; no crash. | always on |
| CT1-0263 | Crashes & out-of-bounds | A voxel vehicle that sinks far under the ground (e.g. killed while lifted by a Magnetron) can crash the game while drawing its shadow. | No crash. | always on |
| CT1-0265 | Crashes & out-of-bounds | Long lists in rulesmd.ini (e.g. AllyParaDropInf with many entries) are cut off after about 127 characters, which can break the list or create bogus types. | Lists up to 2047 characters are read in full. | always on |
| CT1-0266 | Crashes & out-of-bounds | Long lists on units, warheads and weapons (e.g. Dock=, AnimList=) are cut off after about 127 characters; entries past the cut are ignored. | Lists up to 2047 characters are read in full. | always on |
| CT1-0267 | Crashes & out-of-bounds | Trigger [Events] / [Actions] lines longer than 511 characters are cut off, so the last actions are lost or misread. | Long trigger lines are read in full. | always on |
| CT1-0268 | Crashes & out-of-bounds | Country lists longer than about 127 characters (Owner=, RequiredHouses=, a map's Allies=) are cut off; countries at the end are dropped. | The full list is read. | always on |
| CT1-0269 | Crashes & out-of-bounds | A very long sidebar tooltip (long unit name or cost text, mods) overruns its buffer and can corrupt memory. | The tooltip is cut at 65 characters. | always on |
| CT1-0272 | Crashes & out-of-bounds | A missing theater tile file (or a too long tile set file name) crashes the game when the map loads. | The game exits cleanly with an error naming the tile set. | always on |
| CT1-0273 | Crashes & out-of-bounds | On computers with a very high-resolution performance timer, the game can crash at start-up (division by zero). | The start-up speed check no longer divides by zero. | always on |
| CT1-0277 | Crashes & out-of-bounds | An animation that should create infantry where none can be placed (water, cliffs) keeps creating objects every frame until the game ends. | The animation ends; nothing is leaked. | always on |
| CT1-0284 | Crashes & out-of-bounds | An animation with TrailerAnim and TrailerSeperation=0 crashes the game (Internal Error). | No trailer is spawned; no crash. | always on |
| CT1-0288 | Crashes & out-of-bounds | Placing a building without a BuildCat and then losing or adding a Construction Yard leaves the sidebar pointing to deleted data, which can crash the game later. | The sidebar is cleared first; no crash. | always on |
| CT1-0294 | Crashes & out-of-bounds | Blended rectangles (tooltips, selection boxes) near the screen edge at low resolutions write past the screen buffer, causing garbage or crashes. | They are clipped to the screen. | always on |
| CT1-0295 | Crashes & out-of-bounds | Healing warheads with an AnimList pick an animation from outside the list (random animation or crash). | The animation is chosen by the absolute damage. | always on |
| CT1-0296 | Crashes & out-of-bounds | An empty SovParaDropInf list in rulesmd.ini can crash the game when Boris calls an airstrike. | The airstrike launches normally. | always on |
| CT1-0299 | Crashes & out-of-bounds | Erasing a building with a Chrono weapon while infantry walk to garrison it can crash the game. | No crash. | always on |
| CT1-0300 | Crashes & out-of-bounds | Writing the out-of-sync report after a desync can crash the game when a house has an empty factory. | The report is written. | always on |
| CT1-0301 | Crashes & out-of-bounds | With more than 512 building types (mods), building statistics are written past their table, giving wrong statistics and later crashes. | Types above 512 are not counted; nothing is overwritten. | always on |
| CT1-0302 | Crashes & out-of-bounds | Reinforcement trigger actions (7, 80, 107) for a player slot that does not exist or was defeated can crash the game. | No reinforcements are sent. | always on |
| CT1-0304 | Crashes & out-of-bounds | Mind-controlling a unit whose "attacked" trigger removes its own tag crashes the game. | No crash. | always on |
| CT1-0310 | Crashes & out-of-bounds | Units keep a pointer to a mind controller that was removed without releasing them, which can crash the game later. | The pointer is cleared. | always on |
| CT1-0312 | Crashes & out-of-bounds | Selling a building of a side whose only survivor type is the Engineer can freeze the game. | One Engineer comes out; no freeze. | always on |
| CT1-0313 | Crashes & out-of-bounds | With an empty crew list (AlliedCrew, SovietCrew or ThirdCrew), destroying that side's buildings can crash the game. | No survivor, no crash. | always on |
| CT1-0318 | Crashes & out-of-bounds | Loose PIPBRD.SHP / PIPS.SHP / PIPS2.SHP / TALKBUBL.SHP files in the game folder are freed the wrong way, which can corrupt memory or crash on exit. | They are freed correctly. | always on |
| CT1-0322 | Crashes & out-of-bounds | Units at negative cell positions (e.g. aircraft entering from the top or left map edge) write threat values outside their table, corrupting memory (no direct visible effect). | Nothing is written outside the table. | always on |
| CT1-0324 | Crashes & out-of-bounds | Houses without a map edge get a garbage Edge= value when a map is written (e.g. by the random map generator). | "none" is written. | always on |
| ↪ CT1-0346 | Crashes & out-of-bounds | Destroying an airfield with several aircraft stuck on it can crash the game. | The crash path is blocked by the fix of CT1-0008; the reported trigger itself was not reproduced. | covered |
| CT1-0381 | Crashes & out-of-bounds | A Terror Drone whose victim dies at once (e.g. at a factory door) can end up stuck at the top corner of the map. | It dies instead. | always on |
| CT1-0397 | Crashes & out-of-bounds | The game can hang when it looks for a passenger that is not in its transport's passenger list (seen with large open-topped transports). | The search ends; the passenger fires from its own firing point. | always on |
| CT1-0398 | Crashes & out-of-bounds | Very large voxel ships can crash the game while sinking (Internal Error at 004AF313). | No crash. | always on |
| CT1-0404 | Crashes & out-of-bounds | Very large maps (width + height = 512) can crash at random while loading or when walls are built. | No crash. | always on |

### Multiplayer sync (4)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0085 | Multiplayer sync | Resting the mouse on a selected MCV or other deployer can make that machine handle units in a different order and desync a multiplayer game. | Hovering no longer changes the game state; no desync. | always on |
| CT1-0139 | Multiplayer sync | Hovering the mouse over a moving teleport-locomotor MCV (deploy cursor) can cause a reconnection error in multiplayer. | The deploy cursor no longer touches a moving unit; no desync. | always on |
| CT1-0223 | Multiplayer sync | A Gap Generator next to the map edge combined with a Spy Satellite can desync a multiplayer game. | The shroud stays the same on all machines; no desync. | always on |
| CT1-0274 | Multiplayer sync | Building destruction animations (DestroyAnim) are not drawn in the owner's colour, which was also reported to cause reconnection errors. | They are drawn in the owner's colour. | always on |

### Pathfinding & movement (54)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0013 | Pathfinding & movement | A land building with Naval=yes and WaterBound=no can only be placed on water (mods only). | Such a building is placed by the normal land rules; WaterBound buildings are unchanged. | always on |
| CT1-0034 | Pathfinding & movement | Aircraft and jumpjet units ignore speed bonuses (country SpeedAircraftMult / SpeedUnitsMult, veteran FASTER, speed crates). | The speed bonuses apply to them too. | always on |
| CT1-0061 | Pathfinding & movement | A WaterBound building that undeploys into a ship looks for its target cell with land rules, so the ship moves onto land or fails. | The target cell is chosen with the vehicle's own movement rules. | always on |
| CT1-0070 | Pathfinding & movement | DeployToFire units ignore the placement rules of the building they deploy into: ships never deploy on water, and land units sometimes stay put where the building cannot be placed. | The building's own placement rules are checked; the unit deploys in place or moves to a spot where it can. | always on |
| CT1-0078 | Pathfinding & movement | Jumpjet units shot down over a building slow down at roof height and settle on the roof before dying. | They keep falling at crash speed to the ground. | always on |
| CT1-0095 | Pathfinding & movement | Burrowed subterranean units can deploy-fire or deploy while underground or digging. | They surface first, then deploy. | always on |
| CT1-0097 | Pathfinding & movement | Very fast walking, mech or tunnel units jitter around their destination and get stuck. | They arrive normally. | always on |
| CT1-0106 | Pathfinding & movement | A unit that was switched off (EMP, unpowered Robot Tank) and then chronoshifted or lifted by a Magnetron stays unable to move after it is set down, even though it has power again. | The unit's own movement is switched back on when it is set down. | always on |
| CT1-0109 | Pathfinding & movement | Subterranean (tunnelling) units ignore speed bonuses (veterancy, country bonus, speed crates) while underground. | Speed bonuses apply underground too. | always on |
| CT1-0110 | Pathfinding & movement | Aircraft that are allowed onto water by their SpeedType / MovementZone get their move orders onto water changed to a nearby shore. | The aircraft's own SpeedType / MovementZone decide where it can go. | always on |
| CT1-0111 | Pathfinding & movement | A Terror Drone or Squid that misses its target vanishes if another unit has moved onto the cell it jumped from. | It comes out on a free cell nearby. | always on |
| CT1-0112 | Pathfinding & movement | Bunkerable vehicles with unsuitable locomotors (hover, ship, fly, rocket, mech ...) can enter a Tank Bunker, which breaks. | Only drive, walk, tunnel, teleport and jumpjet vehicles may enter. | always on |
| CT1-0113 | Pathfinding & movement | A unit chronoshifted onto an uncrushable unit blows itself up but leaves an invisible barrier on its old cell. | The old cell is freed. | always on |
| CT1-0115 | Pathfinding & movement | A building's FreeUnit looks for a spawn cell with wheeled-unit rules, so hover or amphibious free units can fail to appear or appear where they cannot move. | The free unit's own SpeedType is used. | always on |
| CT1-0116 | Pathfinding & movement | Naval starting units can be placed on land. | They are placed only on cells where they can move. | always on |
| CT1-0117 | Pathfinding & movement | Whether a building that undeploys into a vehicle can move onto a cell is judged with the building's movement rules, not the vehicle's. | The vehicle's SpeedType and MovementZone decide. | always on |
| CT1-0125 | Pathfinding & movement | Units with MovementZone=AmphibiousDestroyer or AmphibiousCrusher cannot enter naval buildings that accept them. | They can enter. | always on |
| CT1-0127 | Pathfinding & movement | A spawner standing on a bridge cannot take its spawns back; they hover above it forever (mods only). | The spawns land on it and dock. | always on |
| CT1-0133 | Pathfinding & movement | When infantry on the shore call a transport ship, the first one to call gives up and stays behind while the others board. | The first caller waits and boards too. | always on |
| CT1-0137 | Pathfinding & movement | Infantry refused at a full transport (e.g. many GIs ordered into one IFV) can leave an invisible impassable barrier on that cell. | No barrier is left; passengers follow a transport that moves away. | always on |
| CT1-0138 | Pathfinding & movement | A teleporting unit (e.g. Chrono Legionnaire) boarding a transport on a bridge leaves an invisible barrier that can freeze or crash the game. | No barrier: boarding on the bridge is refused or waits, and teleporting into a transport leaves no mark. | always on |
| CT1-0145 | Pathfinding & movement | A water-only unit that ends up on an elevated bridge deck (e.g. chronoshifted there) can drive along the bridge. | It is destroyed when it stops on the deck, as when chronoshifted onto land. | always on |
| CT1-0165 | Pathfinding & movement | A hover unit lifted by a Magnetron off a high bridge cannot be selected or damaged afterwards. | It can be selected and damaged. | always on |
| CT1-0172 | Pathfinding & movement | Hovering simple deployers with DeployToLand=no still land when they stop (mods only). | They hover in place. | always on |
| CT1-0178 | Pathfinding & movement | Units sent to attack, capture or move onto a building with a dock (Naval Yard, Service Depot, Helipad) head for the dock point instead. | They head for the building itself. | always on |
| CT1-0179 | Pathfinding & movement | A jumpjet vehicle keeps flying around and ignores orders after its berserk state ends. | It stops and takes new orders. | always on |
| CT1-0180 | Pathfinding & movement | After infantry walk through a cell with a tree, that cell becomes impassable for other players' infantry. | The cell is free for everyone once the infantry has left. | always on |
| CT1-0186 | Pathfinding & movement | A vehicle dropped onto infantry can delete everything in that cell (other vehicles, trees, even itself) without a death. | Only the infantry is crushed; other objects stay. | always on |
| CT1-0188 | Pathfinding & movement | Pre-placed aircraft outside the visible map can be flagged as crashing while the map loads and later cannot die properly. | They are not flagged while the map loads and die normally. | always on |
| CT1-0194 | Pathfinding & movement | A unit ordered to attack a target closer than its weapon's MinimumRange walks all the way out to maximum range. | It backs off just beyond the minimum range. | always on |
| CT1-0205 | Pathfinding & movement | Units chronoshifted to a spot with no free land nearby appear at the top corner of the map. | They stay where they were. | always on |
| CT1-0211 | Pathfinding & movement | Jumpjet units given a move order keep their move mission while hovering at the spot and do not return to guarding. | The move ends near the destination and the unit goes idle. | always on |
| CT1-0214 | Pathfinding & movement | Player units (especially jumpjet and hover units) must come to a full stop before a new order such as attack or guard starts. | The new order starts at once; a queued unload still waits. | always on |
| CT1-0219 | Pathfinding & movement | A jumpjet-type Magnetron beam pulling a BalloonHover unit drops it on top of the firer. | The target lands on a free cell next to the firer. | always on |
| CT1-0225 | Pathfinding & movement | BalloonHover units (hovering jumpjet units) spread and stop as if they were on the ground: groups bunch up at cliffs and buildings. | Hovering units spread across cliffs and can stop over any cell. | always on |
| CT1-0230 | Pathfinding & movement | A unit that changes owner inside a tunnel gets stuck or leaves a blocked cell when it comes out. | It comes out normally. | always on |
| CT1-0254 | Pathfinding & movement | Aircraft on their final landing approach (e.g. a Harrier returning to its airfield) ignore new orders until they have landed. | Only carryalls are held; other aircraft take new orders during the approach. | always on |
| CT1-0262 | Pathfinding & movement | Units crowding onto a bridge can be placed into the cell beside a bridge end and get stuck or switch layers. | That cell is never used from the bridge. | always on |
| CT1-0283 | Pathfinding & movement | Damaged computer units drive to a repair facility that will not take them (unpowered, busy, or the wrong kind) and wait there. | Only a repair facility that accepts them is chosen. | always on |
| CT1-0291 | Pathfinding & movement | A Terror Drone that leaves a destroyed host high above the ground pops out on the ground below. | It falls from where it is and explodes on landing. | always on |
| CT1-0292 | Pathfinding & movement | A Terror Drone or Squid inside a host keeps its old position (where it jumped in), even when the host moves far away (no direct visible effect). | Its position follows the host. | always on |
| CT1-0293 | Pathfinding & movement | Amphibious teleporting vehicles sink when they teleport onto water. | They stay afloat. | always on |
| CT1-0308 | Pathfinding & movement | Deactivated teleporting vehicles (no power, no control centre) can still move and may block a cell forever. | They do not move. | always on |
| CT1-0335 | Pathfinding & movement | Units in tunnels whose entrances differ in height by more than one level shake, sink or get stuck. | They come out and land on the exit cell. | always on |
| CT1-0339 | Pathfinding & movement | Very fast hover units circle at corners, wobble on straight lines and leave cells marked as occupied. | They turn on the spot, drive straight and free the cells they leave. | always on |
| CT1-0344 | Pathfinding & movement | A vehicle that deploys into a building larger than one cell places it off-centre, and is outside the building's cell when it undeploys (mods). | The vehicle's cell is the building's centre cell; normal MCVs are unchanged. | always on |
| CT1-0353 | Pathfinding & movement | Several damaged vehicles sent to a Service Depot together can get stuck on the pad and never be repaired. | They are repaired one after the other. | always on |
| CT1-0359 | Pathfinding & movement | Transports standing on a passable building cell (gate, bib, invisible building) cannot load or unload. | Loading and unloading work there. | always on |
| CT1-0361 | Pathfinding & movement | Computer ships ordered to attack a target far inland drive back and forth along the coast forever. | They give up after one try and the team script moves on. | always on |
| CT1-0386 | Pathfinding & movement | Training Chrono Legionnaires at a Barracks with a rally point leaves a cell of the Barracks permanently blocked. | The cell is freed. | always on |
| CT1-0387 | Pathfinding & movement | A Chrono Legionnaire moved by the Chronosphere and then ordered to move leaves its old cell permanently blocked. | The cell is freed. | always on |
| CT1-0389 | Pathfinding & movement | A tank chronoshifted just as a Magnetron lifts it can never be selected again and stays attached to the beam. | A tank being lifted is not chronoshifted. | always on |
| CT1-0392 | Pathfinding & movement | Infantry with arcing weapons attacking a large building walk around it endlessly and fire only now and then. | They stop at a spot well inside their range and keep firing. | always on |
| CT1-0394 | Pathfinding & movement | Jumpjet units without BalloonHover sink, stop and climb again at every waypoint of a waypoint path. | They keep cruising height through the waypoints and land only at the last one. | always on |

### Targeting & combat (56)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0009 | Targeting & combat | A cloaked Desolator (Cloakable=yes on DESO) that is deployed never fires its deploy weapon again once cloaked. | The deployed Desolator uncloaks, fires its radiation pulse and cloaks again after reloading. | always on |
| CT1-0015 | Targeting & combat | A DeathWeapon only deals its damage at the dying unit: no projectile explosion, no Cluster or Airburst effects. | The DeathWeapon explodes like a projectile that hit, with all of its projectile effects. | always on |
| CT1-0018 | Targeting & combat | A jumpjet vehicle in the air does not turn to face a target behind or beside it, so it often never fires. | It turns towards the target and fires; while cruising it waits as before. | always on |
| CT1-0019 | Targeting & combat | A jumpjet unit that has been shot down keeps firing at its target while it falls. | It stops firing as soon as it starts to crash. | always on |
| CT1-0021 | Targeting & combat | Jumpjet units with Sensors= (e.g. Rocketeers given SensorsSight) never reveal cloaked units under them: the sensor area stays where they took off. | The sensor area follows the flying unit and reveals cloaked units below it. | always on |
| CT1-0025 | Targeting & combat | A mind-control weapon with InfiniteMindControl=yes and Damage=1 can only control one unit; taking a second releases the first. | It can control any number of units. | always on |
| CT1-0026 | Targeting & combat | Non-fighter and strafing aircraft fire their weapon's full Burst only on the first pass; later passes fire a single shot. | Every pass fires the full Burst. | always on |
| CT1-0027 | Targeting & combat | DeployFire vehicles always use their primary weapon, ignore FireOnce (they keep firing until given another order) and cannot be stopped while deploy-firing. | DeployFireWeapon is respected (secondary if the primary cannot fire), FireOnce fires once then guards, Stop works, and repeated deploy orders right after a shot are ignored briefly. | always on |
| CT1-0028 | Targeting & combat | A vehicle that deploys into a building to fire (DeployToFire) forgets its target; the building does not attack it. | The deployed building goes on attacking the vehicle's target. | always on |
| CT1-0042 | Targeting & combat | If the firer dies before its projectile lands (e.g. a Kirov shot down while its bombs fall), the kill counts for nobody: no score, no "destroyed by house" trigger. | The kill is credited to the firer's house. | always on |
| CT1-0043 | Targeting & combat | Open-topped transports (e.g. IFV) judge their attack range by the passengers' primary weapons, not the weapons they actually fire from inside (OpenTransportWeapon). | They use the weapon each passenger really fires from inside. | always on |
| CT1-0048 | Targeting & combat | Invisible (Inviso=yes) projectiles with Airburst=yes or an EMEffect warhead detonate where the projectile is, often missing moving targets. | They snap to the target and hit. | always on |
| CT1-0051 | Targeting & combat | Units firing at buildings with a TargetCoordOffset (e.g. a Destroyer shelling a Naval Yard) aim at the building centre, so they keep turning or miss; target lines point there too. | Fire angle and target line use the building's target point. | always on |
| CT1-0058 | Targeting & combat | LandTargeting / NavalTargeting do not stop units from targeting trees or empty water cells, and NavalTargeting=7 / LandTargeting=2 units still use their primary weapon on trees on land. | Those targets are refused as the settings say, and the secondary weapon is used against trees on land. | always on |
| ⚙ CT1-0071 | Targeting & combat | Arcing projectiles (artillery) fired at targets higher than the firer always launch at 45 degrees and overshoot targets up a cliff. | When switched on, they use the correct ballistic angle and hit uphill targets. Off by default (original behaviour). | `ArcingProjectilesAimUphillExactly` (off by default) |
| CT1-0074 | Targeting & combat | Missiles launched by spawners (Dreadnought) follow a moving target unit and explode where it has moved to. | They fly to the cell the target stood in at launch (buildings unchanged); a fast unit can escape. | always on |
| CT1-0079 | Targeting & combat | Railgun beams (and their AmbientDamage) and flame particles are cut off where the line dips under rising ground. | They reach the target and stop only at the first cliff or wall. | always on |
| CT1-0090 | Targeting & combat | Units and buildings whose weapon has DecloakToFire=no stay visible while aiming and reloading. | They cloak while aiming and reloading and keep firing cloaked. | always on |
| CT1-0092 | Targeting & combat | Long-range anti-air and big-radius warheads (e.g. nukes) miss some aircraft at certain offsets, especially on small maps. | All aircraft in range are found. | always on |
| CT1-0093 | Targeting & combat | Anti-air weapons cannot fire at aircraft when both are over a bridge. | They fire normally. | always on |
| CT1-0101 | Targeting & combat | Split projectiles from an AirburstWeapon forget their weapon, so effects such as radiation are lost. | Each split impact applies the weapon's effects (e.g. radiation). | always on |
| CT1-0104 | Targeting & combat | Objects with AttackFriendlies=yes (especially computer buildings) lose their friendly target every frame and never fire. | They attack the friendly target. | always on |
| CT1-0124 | Targeting & combat | A voxel barrel with FireAngle is drawn at the wrong angle right after the unit is built or unloaded. | It is drawn at FireAngle from the first frame. | always on |
| CT1-0126 | Targeting & combat | An Aircraft Carrier under or next to an elevated bridge gives up its attack instead of moving to a better spot; flying carriers near bridges never attack. | It moves to find a firing position; flying ones attack. | always on |
| CT1-0130 | Targeting & combat | Healing weapons that can hit air never pick damaged flying units by themselves. | They heal flying units automatically. | always on |
| CT1-0147 | Targeting & combat | Electric-assault secondary weapons (Tesla Trooper) never charge an ally's Tesla Coil by themselves, but keep targeting own overpowerable buildings they cannot affect. | They charge allied coils automatically and ignore buildings they cannot affect. | always on |
| CT1-0148 | Targeting & combat | Area damage on a bridge misses units on the ground below (and the other way round); aircraft hurt themselves despite DamageSelf=no; aircraft landing or taking off take area damage twice. | Both levels are hit, DamageSelf is respected, damage is applied once. | always on |
| CT1-0151 | Targeting & combat | Chrono Legionnaires firing out of a Battle Fortress at a large building keep dropping the warp, because distance is measured to the building centre. | The warp holds and the building is erased. | always on |
| CT1-0156 | Targeting & combat | Vertical=yes projectiles dropped by aircraft fly off sideways or towards the target. | They fall straight down. | always on |
| CT1-0159 | Targeting & combat | Infantry with DeployFireWeapon=0 (deploy-firing infantry such as a modded Desolator) fire their secondary weapon when deployed. | DeployFireWeapon is respected (e.g. 0 = primary). | always on |
| CT1-0160 | Targeting & combat | Passengers of an open-topped vehicle frozen by a Chrono weapon keep erasing their target, and a frozen Magnetron keeps holding its victim. | Both stop when the vehicle is frozen. | always on |
| CT1-0166 | Targeting & combat | Infantry and buildings with a Magnetron-type weapon keep holding their victim after they stop or switch targets; a Magnetron ordered onto a new target keeps holding the old one. | The victim is dropped as soon as the firer's target changes. | always on |
| CT1-0169 | Targeting & combat | Vehicles that cannot move (Speed=0 or MovementRestrictedTo) chase and retaliate against targets out of range; Hunt does nothing and Area Guard locks on far targets. | They only engage targets in range; Hunt turns into Guard and Area Guard scans in place. | always on |
| CT1-0175 | Targeting & combat | Jumpjet units with a missile spawner never launch while hovering. | They launch while hovering (not while flying). | always on |
| ⚙ CT1-0183 | Targeting & combat | Anti-air-only weapons (Patriot Missile System, Flak Cannon) ignore falling units such as paratroopers. | When switched on, they also shoot falling units. Off by default (original behaviour). | `AAOnlyWeaponsTargetFallingUnits` (off by default) |
| CT1-0196 | Targeting & combat | Normal units cannot force-fire at cloaked allied units without a sensor unit nearby. | Force-fire on them is allowed. | always on |
| CT1-0197 | Targeting & combat | V3 rockets and sea missiles fired at targets on high cliffs or under bridges level off too early and fly into the cliff or bridge. | They climb until they are high enough above the target too. | always on |
| CT1-0200 | Targeting & combat | Weapon selection only checks the primary weapon for a Magnetron-type warhead, so such a beam is used on vehicles in a Tank Bunker. | Both weapons are checked; the other weapon is used where the beam cannot work. | always on |
| CT1-0201 | Targeting & combat | A Magnetron can pull a vehicle out of a Tank Bunker. | Vehicles in a Tank Bunker cannot be targeted with it. | always on |
| CT1-0217 | Targeting & combat | A unit attacked by several equally threatening enemies keeps switching targets each time it is hit. | It keeps its target unless the new attacker is more threatening. | always on |
| CT1-0228 | Targeting & combat | Units in Area Guard wait much longer than other units before attacking the next target after a kill. | They pick the next target as quickly as in other missions. | always on |
| CT1-0234 | Targeting & combat | Turreted defences with an OmniFire=yes weapon still turn their turret before firing. | They fire in any direction at once. | always on |
| CT1-0239 | Targeting & combat | A vehicle given a new target while its turret is turning back finishes turning back first. | The turret turns straight to the new target. | always on |
| CT1-0257 | Targeting & combat | Passengers firing out of a cloaked open-topped transport do not uncloak it, even with DecloakToFire=yes weapons. | The transport uncloaks when a passenger fires. | always on |
| CT1-0276 | Targeting & combat | Chrono weapons fired at Warpable=no units still have side effects (a carrier's planes die, mind-controlled units are freed). | Nothing happens to Warpable=no units. | always on |
| CT1-0280 | Targeting & combat | Damaging animations (Damage= on an animation) use a fixed fire warhead and no owner: kills are credited to nobody and allies are hurt. | They use their own Warhead and belong to their owner. | always on |
| CT1-0282 | Targeting & combat | Aircraft attacking ground targets with non-arcing weapons measure range including their altitude, so they must get closer before firing. | They fire at their full horizontal range. | always on |
| CT1-0298 | Targeting & combat | An aircraft whose target dies during its attack can crash the game. | The aircraft carries on; no crash. | always on |
| CT1-0303 | Targeting & combat | Particle damage (railgun lines, flames, gas) is credited to nobody: no experience, no retaliation, allies are hurt. | The damage is credited to the firer and its house. | always on |
| CT1-0320 | Targeting & combat | Cloaked infantry ordered to attack a distant target uncloak at once and walk up visible. | They stay cloaked until in range, then uncloak to fire. | always on |
| CT1-0323 | Targeting & combat | An open-topped transport with Gunner=yes carrying a Chrono Legionnaire crashes the game when the passenger fires. | The passenger does not fire (the vehicle uses its own chrono weapon); no crash. | always on |
| CT1-0338 | Targeting & combat | Spawned aircraft (e.g. repair drones) keep circling their target after the job is done instead of returning. | They return within moments and pick new targets normally. | always on |
| CT1-0340 | Targeting & combat | Units with IsChargeTurret=yes can only ever use Weapon1. | They can select their other weapons (e.g. an anti-air Weapon2). | always on |
| CT1-0356 | Targeting & combat | Passengers of a chronoshifted open-topped transport keep firing while the transport itself is still locked. | Passengers also wait until the lock ends. | always on |
| CT1-0379 | Targeting & combat | GI-type infantry fire their deployed weapon before the deploy animation has finished. | They fire only once deployed. | always on |
| CT1-0385 | Targeting & combat | Passengers of open-topped transports get no high-ground range bonus (SubjectToElevation=yes). | They get the bonus like units on the ground. | always on |

### AI (25)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0030 | AI | AI triggers whose condition is an upgrade building (e.g. "owns at least 1") never fire. | They fire once the upgrade is installed. | always on |
| CT1-0067 | AI | The computer cannot build ships and land vehicles at the same time: while it wants a ship its War Factory idles, and the other way round. | The War Factory and Naval Yard of the computer produce in parallel, like a human player's. | always on |
| CT1-0089 | AI | Objects placed outside the visible map area pull the starting view and the computer's base centre towards them. | They are ignored for both. | always on |
| CT1-0108 | AI | A computer team with jumpjet members sent to a cell with a building never finishes its move: the units hover on the roof and the team is stuck. | Arrival is judged on the ground plane, so the team carries on. | always on |
| CT1-0132 | AI | Computer units looking for a Tank Bunker, Bio Reactor and the like can pick one they cannot reach and get stuck. | They pick the nearest reachable one, or none. | always on |
| CT1-0142 | AI | A computer player that defeats its enemy, or becomes allied with it, has no new enemy until much later. | It picks a new enemy right away. | always on |
| ⚙ CT1-0161 | AI | Computer-controlled vehicles move one cell aside every time they finish cloaking (the original does this on purpose to make them harder to find). | When switched on, they stay where they are after cloaking. Off by default (original behaviour). | `AICloakedUnitsDontScatter` (off by default) |
| CT1-0182 | AI | The computer's base goes on alert when its buildings take friendly fire or zero damage. | Only real enemy damage alerts it. | always on |
| CT1-0192 | AI | Computer team scripts that search the whole map never pick hovering jumpjet units as targets. | Air units are considered too. | always on |
| CT1-0199 | AI | Computer players can become angry with friendly houses, and with no enemy around they pick the first house in the list instead of the nearest. | Anger is only set against enemies, and the nearest enemy is chosen. | always on |
| CT1-0207 | AI | Computer team scripts for Chronoshift (56, 57) and the Iron Curtain fire whichever superweapon sits at a fixed position in the list (wrong in mods). | They use the house's actual Chronosphere, Chrono Warp and Iron Curtain. | always on |
| CT1-0208 | AI | Uncaptured tech buildings (NeedsEngineer) owned by the neutral house count as threats by their ThreatPosed. | Their threat is 0 while uncaptured. | always on |
| CT1-0210 | AI | Computer infantry walk into garrisonable civilian buildings during normal auto-targeting. | They only garrison when hunting, by team script or attack-move. | always on |
| CT1-0212 | AI | A computer team sent to garrison a building that can no longer be garrisoned (e.g. the player got there first) keeps walking to it. | The team picks another building at once, or goes idle. | always on |
| CT1-0246 | AI | A team script "Deploy" for a non-MCV deployer (e.g. Slave Miner) gets stuck forever if the building does not fit where it stands. | It drives to a clear spot and deploys. | always on |
| CT1-0247 | AI | IFVs and open-topped transports in teams created by trigger actions 7, 80 and 107 do not use their passengers' weapons. | They work as normal IFVs and open-topped transports. | always on |
| CT1-0248 | AI | Computer teams are often left underfilled because recruiting and building disagree about which units are free. | Teams fill up as intended. | always on |
| CT1-0306 | AI | Buildings mind-controlled by the computer are sent on Hunt, which does nothing for buildings. | They are set to Guard. | always on |
| CT1-0358 | AI | The computer keeps rebuilding walls around the old spot of a building that has been rebuilt elsewhere. | Walls are built around the new location only. | always on |
| CT1-0362 | AI | When a computer base loses buildings to Engineers, new buildings keep growing from the captured ones towards the enemy. | Lost buildings are no longer used as placement anchors. | always on |
| CT1-0363 | AI | Computer MCVs with a jumpjet, teleport or hover locomotor never deploy at the start of the game. | They deploy right away. | always on |
| CT1-0368 | AI | The computer building many tanks in a row can leave tanks stuck inside its War Factory, unselectable and unable to move. | Tanks leave the factory normally. | always on |
| CT1-0382 | AI | Computer units with an anti-air secondary weapon (Flak Track, Apocalypse) are never sent to defend the base against air attackers. | They are sent like units with an anti-air primary. | always on |
| ⚙ CT1-0393 | AI | Vehicles with CloakStop=yes stay cloaked while moving; the setting has no effect on moving units. | When switched on, they uncloak while moving and cloak again when they stop (needs AICloakedUnitsDontScatter=yes too). Off by default (original behaviour). | `CloakStopUnitsUncloakWhileMoving` (off by default) |
| CT1-0402 | AI | A computer MCV sent by a team script to deploy at a waypoint checks the wrong cells for the building, so it never deploys or keeps trying. | The same cells as the real deploy are checked and cleared; it deploys. | always on |

### Units & AI (1)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| ◐ CT1-0407 | Units & AI | Spawners kill their own spawns when they cloak (e.g. a Boomer submerging loses a missile being launched; cloaked carriers crash their Hornets) and when they enter a transport or building. | Cloaking no longer kills the spawns. When switched on, entering a transport or building does not either; destroying or selling the spawner still does. | cloak half: always on; limbo half: `SpawnsSurviveOwnerLimbo` (off by default) |

### Economy & ore (13)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0118 | Economy & ore | Amphibious harvesters do not return by themselves to a WaterBound refinery on water. | They return to it when full. | always on |
| CT1-0135 | Economy & ore | A harvester lifted by a Magnetron while unloading at the refinery can never unload again. | It unloads normally afterwards. | always on |
| CT1-0152 | Economy & ore | Flying (MovementZone=Fly) or jumpjet harvesters sent to a refinery by hand do not dock. | They dock and unload. | always on |
| CT1-0153 | Economy & ore | Jumpjet harvesters leaving the factory just sit there. | They go harvesting. | always on |
| CT1-0189 | Economy & ore | Subterranean harvesters in an area enclosed by water or cliffs never find a refinery. | They return to the refinery underground. | always on |
| CT1-0202 | Economy & ore | Harvesters with Passengers or DeployFire open their doors or fire at the refinery instead of unloading ore. | They unload normally. | always on |
| CT1-0203 | Economy & ore | A harvester that leaves a Tank Bunker cannot move any more. | It leaves and takes orders like any vehicle. | always on |
| CT1-0260 | Economy & ore | Subterranean harvesters sometimes stop harvesting and sit idle in a field with no ore. | They keep harvesting. | always on |
| CT1-0334 | Economy & ore | Hover harvesters built from a War Factory without a rally point stop at the door and do not start harvesting. | They go harvesting by themselves. | always on |
| CT1-0336 | Economy & ore | With several Barracks and many infantry queued, some trained infantry are lost (the money is refunded) when the main Barracks is busy. | They come out of another free Barracks. | always on |
| CT1-0351 | Economy & ore | A Chrono Miner fully repaired at a Service Depot is left with its teleport drive switched off (no visible effect in the unmodded game). | The drive is switched back on when the repair ends. | always on |
| CT1-0372 | Economy & ore | Capturing a tech oil derrick again and again with mind control pays its starting cash bonus each time. | The bonus is paid only on the first capture. | always on |
| CT1-0403 | Economy & ore | Once one ore cell cannot spread, ore stops spreading over the whole map; Ore Drills stop after their first ring. | Spreading continues normally. | always on |

### Triggers & maps (14)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0003 | Triggers & maps | A custom map whose [Preview] / [PreviewPack] sections come after [Map] shows no preview in the map list. | The preview is shown no matter where the sections are in the file. | always on |
| CT1-0023 | Triggers & maps | In a theater with 256 or more tile sets, destroyed NE-SW bridges cannot be repaired. | Such bridges can be repaired. | always on |
| CT1-0045 | Triggers & maps | Vehicles killed by damage with an owner but no attacking unit (radiation, animation warheads) do not count as killed for map triggers. | Such kills spring the "destroyed" triggers. | always on |
| CT1-0084 | Triggers & maps | Waypointing a group with spies / engineers onto a capturable building makes the other infantry walk in too and spring its "entered by" trigger. | Only infantry that can enter goes in; the others stop. | always on |
| CT1-0105 | Triggers & maps | Train cars of pre-placed locomotives are linked to the wrong vehicle when an earlier [Units] line could not be created or carried passengers. | The follower index counts [Units] lines exactly. | always on |
| CT1-0185 | Triggers & maps | Map trigger events 2 (spied by), 53 and 54 (spy as house / as infantry) never fire when a spy enters. | These events fire when a spy enters. | always on |
| CT1-0191 | Triggers & maps | In campaigns where the player controls more than one house, a reveal crate picked up by the other house reveals nothing for the player. | The map is revealed for the player. | always on |
| CT1-0195 | Triggers & maps | Units with Trainable=no created by trigger actions still get the team's veteran / elite level. | They stay rookies. | always on |
| CT1-0227 | Triggers & maps | The "Play Sidebar Movie" trigger actions (100, 117) do nothing outside campaigns. | The movie plays in skirmish and multiplayer maps too. | always on |
| CT1-0244 | Triggers & maps | The team script action "Move to cell" decodes the cell number the old Red Alert way, sending teams to the wrong place. | The cell number is read as 1000 * Y + X. | always on |
| CT1-0249 | Triggers & maps | Ore and gem settings (Value, Growth, Spread) in a map or game mode INI are ignored. | The map's values are used. | always on |
| CT1-0256 | Triggers & maps | A campaign mission started under a differently cased file name shows no mission name or briefing. | The mission name and briefing are found regardless of case. | always on |
| ⚙ CT1-0270 | Triggers & maps | Ordinary explosions never collapse destructible cliffs; only railgun and wave weapons can, although the attack cursor is offered on cliffs. | When switched on, explosions collapse destructible cliffs with the CollapseChance probability. Off by default (original behaviour). | `ExplosionsCollapseCliffs` (off by default) |
| CT1-0343 | Triggers & maps | After a Spy Satellite, RevealOnFire or a reveal crate uncovers the map, "discovered by player" trigger events (4 / 5) never fire again. | They fire when the player actually sees the object. | always on |

### Shroud & fog (1)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0410 | Shroud & fog | Units on height-14 cliff tops are never reported as discovered when the player reveals their cell. | They are discovered like units at any other height. | always on |

### INI & data (2)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0408 | INI & data | TemperateOrePatchLamps / SnowOrePatchLamps lists of the random map generator are cut off after about 127 characters. | The full list is read. | always on |
| CT1-0409 | INI & data | Long Prerequisite= lists and voice lists (e.g. VoiceSelect=) are cut off after about 127 characters, particle ColorList= after 511. | These lists are read up to 2047 characters. | always on |

### Graphics & UI (96)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0002 | Graphics & UI | After loading a savegame, the light of a lit building (lamp posts, lit tech buildings) stays on the ground forever, even after the building is destroyed or sold. | The light goes out when the building is destroyed or sold, also after loading. | always on |
| CT1-0004 | Graphics & UI | The Retint Red / Green / Blue trigger actions wipe out the colour of coloured light sources on the map (only their brightness stays). | Light sources keep their colour after a retint. | always on |
| CT1-0007 | Graphics & UI | Capturing a building that counts as a vehicle (1x1 building with UndeploysInto=, mods only) plays the "building captured" EVA message and radar event. | No "building captured" message for such buildings. | always on |
| CT1-0011 | Graphics & UI | After a "Cannot deploy here" placement failure, the building / defence tab hotkey no longer enters placement mode; the cameo has to be clicked. | The hotkey enters placement mode again after a failed placement. | always on |
| CT1-0012 | Graphics & UI | Clicking the ground with a selected Construction Yard (when it can be packed up) makes EVA say "New rally point established". | No rally point message when the building packs up. | always on |
| CT1-0014 | Graphics & UI | Thick lasers drawn in house colour (e.g. Prism Tower beams with support towers, dark house colours, low detail) lose most of their thickness. | Every layer of the beam is drawn, with a smooth fall-off. | always on |
| CT1-0016 | Graphics & UI | A garrisonable building with more than 10 occupant slots corrupts its own settings when reading MuzzleFlash lines (it can even gain a radar) and draws the extra muzzle flashes in wrong places. | Only 10 MuzzleFlash points are read and nothing else is overwritten; flashes of occupants 11 and up are drawn at the building centre. | always on |
| CT1-0020 | Graphics & UI | A turreted jumpjet unit that stops in the air turns its turret to the bottom-right of the screen. | The turret keeps following the body. | always on |
| CT1-0024 | Graphics & UI | SHP (non-voxel) vehicles ignore the art setting TurretOffset; the turret is drawn at the centre of the body. | The turret is shifted along the body facing, as on voxel vehicles. | always on |
| CT1-0029 | Graphics & UI | With Burst weapons, lasers and similar effects are drawn from the wrong barrel (mirrored fire offset) while the muzzle flash is right. | The effect comes from the same barrel as the muzzle flash. | always on |
| CT1-0032 | Graphics & UI | Vehicles pointed at a Grinder they cannot enter (no power, under construction, being sold) show the normal cursor instead of "No Enter". | The "No Enter" cursor is shown. | always on |
| CT1-0035 | Graphics & UI | Vehicles with a custom Palette= are drawn with the normal unit palette. | They are drawn with their custom palette. | always on |
| CT1-0036 | Graphics & UI | The mind-control ring over a controlled unit disappears when it cloaks and never comes back. | The ring is restored when the unit becomes visible again. | always on |
| CT1-0037 | Graphics & UI | The nuke missile and its payload are always drawn bright, ignoring Bright=no. | Bright=no on these weapons is respected. | always on |
| CT1-0038 | Graphics & UI | The self-healing "+" pip (Hospital / Machine Shop) ignores PixelSelectionBracketDelta, while the health bar moves. | The pip moves together with the health bar. | always on |
| CT1-0039 | Graphics & UI | The health bar of a unit or building frozen by a Chrono weapon stays visible after the mouse moves away. | The bar disappears when the cursor leaves, as for any other unit. | always on |
| CT1-0040 | Graphics & UI | Buildings with SelfHealing=yes keep their damaged look after healing back to full health. | They switch back to the undamaged graphics. | always on |
| CT1-0044 | Graphics & UI | Buildings draw the lasers of their secondary weapon with the primary weapon's laser settings. | Each weapon uses its own laser colours and duration. | always on |
| CT1-0047 | Graphics & UI | Translucent SHP animations (chrono, psychic and warp effects) are drawn with a green tint and colour banding in 16-bit mode. | Exact blending, no green tint. | always on |
| CT1-0050 | Graphics & UI | Railgun beams and particles end at the wrong spot when fired at buildings with TargetCoordOffset or force-fired at bridges; fire-stream particles fired downhill are lifted to the ground. | The beam ends at the real target point (on the bridge deck); fire streams follow their path. | always on |
| CT1-0052 | Graphics & UI | In single-player missions the player cannot see cloaked units and buildings of allied houses at all. | Allied cloaked objects are drawn shadowy, like your own. (Skirmish is unchanged.) | always on |
| CT1-0053 | Graphics & UI | Buildings with a bib and a long build-up animation (e.g. War Factory) flash their bib for a frame or two when the build-up starts. | The bib appears only once the building is placed. | always on |
| CT1-0055 | Graphics & UI | Buildings pre-placed on the map whose type has a NaturalParticleSystem start it from the first frame. | Pre-placed copies get no particle system; the same building built during play still gets one. | always on |
| CT1-0056 | Graphics & UI | Jumpjet units with TiltCrashJumpjet=no are never drawn tilted, not even by explosions or slopes. | They tilt and flip like other vehicles. | always on |
| CT1-0062 | Graphics & UI | Anti-air-only buildings (Patriot Missile, Flak Cannon) show no attack cursor over enemy aircraft and cannot be ordered to fire at them. | The attack cursor is shown and the order works. | always on |
| CT1-0063 | Graphics & UI | Passengers firing out of an open-topped transport whose own weapon has Burst=2 fire from the mirrored side every other volley. | They always fire from their own firing point. | always on |
| CT1-0064 | Graphics & UI | Disguised spies and units are drawn with the wrong palette when the disguise type has its own Palette=. | The disguise is drawn with its own palette in the disguise house colours. | always on |
| CT1-0065 | Graphics & UI | Superweapon buildings pre-placed on the map show their "ready" animation at the start of the match. | They show the normal animation until the superweapon timer is started. | always on |
| CT1-0068 | Graphics & UI | Jumpjet units (e.g. Rocketeers) snap to facing bottom-right when they start moving. | They turn from their current facing. | always on |
| CT1-0069 | Graphics & UI | Objects with their own Palette= are not tinted by lightning storms, nukes or retint triggers when their house colour is in the second half of the colour list. | They are tinted like everything else. | always on |
| CT1-0073 | Graphics & UI | Powered animations of a captured Powered building freeze in their old state. | They follow the new owner's power. | always on |
| CT1-0076 | Graphics & UI | A turreted vehicle hit by EMP keeps turning its turret. | The turret stays still until the EMP wears off. | always on |
| CT1-0077 | Graphics & UI | Hovering jumpjet units keep bobbing up and down under EMP. | They stay level until the EMP wears off. | always on |
| CT1-0081 | Graphics & UI | Chrono Miners (teleport locomotor) and Tunnel units are not tilted on slopes or by explosions. | They tilt like ordinary vehicles. | always on |
| CT1-0083 | Graphics & UI | Carrier spawns (Hornets) land facing a fixed direction instead of lining up with the carrier. | They land facing the same way as the carrier. | always on |
| CT1-0086 | Graphics & UI | A factory being sold or undeployed still draws its rally point line. | The line disappears as soon as it is being sold. | always on |
| CT1-0087 | Graphics & UI | Iron Curtain and berserk tints are missing on SHP vehicles and aircraft, and building animations away from the foundation take no tint or the neighbour's. | They get the same tint colours as voxel vehicles and the building body. | always on |
| CT1-0088 | Graphics & UI | Iron-Curtained buildings show no Iron Curtain tint. | They are tinted with IronCurtainColor; force-shielded ones keep ForceShieldColor. | always on |
| CT1-0091 | Graphics & UI | An ally's Sensors=yes unit reveals your cloaked buildings. | Only enemy sensor units reveal them. | always on |
| ⚙ CT1-0094 | Graphics & UI | Building upgrades show the power-up animation of the wrong slot (always the first upgrade slot, wrong with more than 3 upgrades or on an empty building). | When switched on, each upgrade shows the animation of its own PowersUpToLevel. Off by default (original behaviour). | `UpgradesUsePowersUpToLevelAnim` (off by default) |
| CT1-0098 | Graphics & UI | Animations that spawn infantry (MakeInfantry) with UseNormalLight=no ignore cell lighting and are drawn full bright. | They are lit like the cell. | always on |
| CT1-0100 | Graphics & UI | Nuke and Psychic Dominator map lighting does not affect aircraft. | Aircraft are lit like everything else. | always on |
| CT1-0114 | Graphics & UI | A sensor unit destroyed while moving (or taken over, or after leaving a transport) leaves a permanent sensor area behind that reveals cloaked units there forever. | The sensor area goes away with the unit. | always on |
| CT1-0123 | Graphics & UI | When several electric bolts are on screen, only the first follows the firing vehicle; the others stay where they were fired, and the first jumps between barrels. | Every bolt follows the vehicle from its own barrel. | always on |
| CT1-0128 | Graphics & UI | Flying jumpjet units (Kirov, Floating Disc, Siege Chopper) over an elevated bridge cast their shadow on the ground below the bridge. | The shadow is drawn on the bridge deck. | always on |
| CT1-0129 | Graphics & UI | Lasers, electric bolts and rad beams always end on the target even when the shot is scattered (Inviso=yes with FlakScatter=yes). | The beam ends where the shot actually lands. | always on |
| CT1-0131 | Graphics & UI | Voxel projectiles ignore AnimPalette and FirersPalette. | Voxel projectiles use them like shape projectiles. | always on |
| CT1-0136 | Graphics & UI | Vehicles on ordinary slopes are tilted too steeply. | They are tilted by the correct angle; diagonal slopes are unchanged. | always on |
| CT1-0140 | Graphics & UI | A hover vehicle lifted by a Magnetron casts the wrong shadow. | Its shadow is drawn correctly while lifted. | always on |
| CT1-0146 | Graphics & UI | The airstrike designator line drawn to a lower target is cut off. | The whole line is drawn. | always on |
| CT1-0149 | Graphics & UI | A damaged building keeps smoking after an Engineer repairs it. | The smoke stops. | always on |
| CT1-0150 | Graphics & UI | The TrailerAnim of a voxel animation (debris, meteors) is spawned far above it. | The trail is spawned at the animation. | always on |
| CT1-0157 | Graphics & UI | Engineers show the wrong cursor over buildings that need no repair or carry a bomb (disarm cursor with Alt), and spies show the infiltrate cursor when Ctrl is held. | The cursors match what the click will actually do. | always on |
| CT1-0167 | Graphics & UI | Ships destroyed in the air (lifted by a Magnetron) sink from the air, and naval hover vehicles destroyed on a bridge sink through it. | They explode instead. | always on |
| CT1-0168 | Graphics & UI | While a unit is reloading, it shows the attack cursor over targets it may not attack at all (e.g. a Yuri Clone over a unit that cannot be mind-controlled). | No attack cursor over such targets, reloading or not. | always on |
| CT1-0170 | Graphics & UI | If a map file has a section for a unit type, the barrel settings (BarrelTravel, BarrelRecoil ...) from rulesmd.ini are overwritten by the turret values on that map. | Barrel settings from rulesmd.ini survive the map section; barrels still follow the turret values given in the same section. | always on |
| CT1-0173 | Graphics & UI | A DeployingAnim with Shadow=yes played in the air casts its shadow at the unit's height. | The shadow is drawn on the ground. | always on |
| CT1-0174 | Graphics & UI | DeployingAnims are drawn without the unit's tint and lighting (Iron Curtain, berserk, dark maps). | They take the same tint and lighting as the unit. | always on |
| CT1-0176 | Graphics & UI | In planning mode, a node stays "hovered" for the old unit after selecting another unit with a hotkey. | The hovered node is checked again every frame. | always on |
| CT1-0177 | Graphics & UI | Cloaked jumpjet infantry still cast a shadow. | No shadow while cloaked. | always on |
| CT1-0181 | Graphics & UI | A Mirage Tank disguised as a tree glows with the Iron Curtain or airstrike colour, giving it away. | It looks like a tree. | always on |
| CT1-0184 | Graphics & UI | SHP vehicles with a turret are drawn without the Iron Curtain glow. | They glow like other units. | always on |
| CT1-0193 | Graphics & UI | In multiplayer games, a latency number is drawn at the top left even when the debug display is off. | It is shown only with the debug display on. | always on |
| CT1-0204 | Graphics & UI | A selected mind-controlled unit is dropped from the selection when the Psychic Dominator takes it over. | It stays selected. | always on |
| CT1-0209 | Graphics & UI | Vehicles standing on walls or in open gates show the sell cursor and can be sold. | No sell cursor over them; units can only be sold on a Service Depot. | always on |
| CT1-0226 | Graphics & UI | When a limited unit (Tanya, Boris, Yuri Prime) dies inside a transport, its cameo stays greyed out for a while. | The cameo becomes buildable at once. | always on |
| CT1-0229 | Graphics & UI | Voxel projectiles and voxel animations (missiles, debris) are lit with a single light, darker than voxel vehicles. | They are lit like voxel vehicles. | always on |
| CT1-0231 | Graphics & UI | Shadows of non-aircraft units with the Fly locomotor, and of aircraft dragged by a Magnetron, are drawn in the wrong place. | The shadows are drawn on the ground under the unit. | always on |
| CT1-0237 | Graphics & UI | A computer's Construction Yard plays no ProductionAnim when it places a finished building. | It plays the animation, as for a human player. | always on |
| CT1-0242 | Graphics & UI | Observers cannot see Ivan bombs planted by players. | Observers see every bomb. | always on |
| CT1-0250 | Graphics & UI | Typing in chat or name boxes with a non-Latin keyboard (e.g. Russian, Polish) without an IME gives wrong letters. | The right letters appear (system code page). | always on |
| CT1-0251 | Graphics & UI | The info tip and the spied production cameo of a selected building are drawn under units and at the building's foot. | They are drawn on top, at the middle of the building. | always on |
| CT1-0252 | Graphics & UI | Adding or removing units in a queue that is on hold does not update the number on the cameo. | The number updates at once. | always on |
| CT1-0253 | Graphics & UI | The mouse cursor is redrawn only every 16 ms, so it lags behind fast movement. | It is redrawn every 1 ms (limited by the Windows timer). | always on |
| CT1-0255 | Graphics & UI | Remappable AltPalette animations always use the first house colour. | They use the owner's colour. | always on |
| CT1-0258 | Graphics & UI | Infantry firing their secondary weapon while standing in water play their land firing animation. | They use the water attack animation, as with the primary weapon. | always on |
| CT1-0271 | Graphics & UI | At slow game speeds, lasers, particles, spotlights and other effects are switched off as if the computer were too slow. | The game speed is taken into account, so the effects stay on. | always on |
| CT1-0285 | Graphics & UI | With ShakeScreen=0 in rulesmd.ini, destroying any building crashes the game. | No screen shake, no crash. | always on |
| CT1-0290 | Graphics & UI | A mind-controlling unit that is also a parasite still draws its control link while riding inside its victim (mods). | No link is drawn while it is inside. | always on |
| CT1-0297 | Graphics & UI | All walls are drawn in the local player's colour, regardless of owner. | Each wall is drawn in its owner's colour. | always on |
| CT1-0305 | Graphics & UI | A building that is destroyed shows a single green pip in its health bar for a moment. | The last pip is red. | always on |
| CT1-0317 | Graphics & UI | Infantry without Pip= / OccupyPip= show yellow or white pips in transports and garrisons instead of green. | The default green pips are shown. | always on |
| CT1-0321 | Graphics & UI | The power-toggle cursor is offered on buildings that are under EMP or being chronoshifted, and the click works. | The no-toggle cursor is shown on them. | always on |
| CT1-0327 | Graphics & UI | Clicking the ground while a Construction Yard is being sold can make it pack up into an MCV instead of being sold. | Clicks are ignored while it is being sold; it is sold. | always on |
| CT1-0328 | Graphics & UI | Units in open-topped transports (Battle Fortress, IFV) show the "behind building" marker. | The marker is not shown for them. | always on |
| CT1-0332 | Graphics & UI | With fog of war on (spawner FogOfWar=yes): enemy units stay visible under fog, hovering units lose vision, height-14 cells never clear, fogged buildings are drawn broken and cannot be targeted. | Units vanish under fog, air units keep vision, all heights clear, fogged buildings look and click like visible ones. (A drawing glitch under fog is not fixed.) | always on |
| CT1-0347 | Graphics & UI | Animations drawn in the unit palette (deploy and mutation animations) ignore UseNormalLight=no and are full bright at night. | They are lit like the cell. | always on |
| CT1-0348 | Graphics & UI | Animations with AltPalette=yes are drawn on top of the black shroud. | They are hidden under the shroud like other animations. | always on |
| CT1-0350 | Graphics & UI | Conventional=yes warheads hitting non-naval units on water (hover and amphibious units) play a water splash instead of an explosion. | The normal explosion plays; ships and submarines are unchanged. | always on |
| CT1-0370 | Graphics & UI | The Force Shield (and airstrike) tint shows through the black shroud as a coloured silhouette of the building. | Shrouded parts stay black. | always on |
| CT1-0371 | Graphics & UI | Non-voxel (SHP) vehicles on slopes fire from a tilted firing point although their picture is not tilted. | They fire from the same point as on flat ground. | always on |
| CT1-0373 | Graphics & UI | The map retint trigger actions also tint building animations with UseNormalLight=yes. | Those animations keep their colours. | always on |
| CT1-0383 | Graphics & UI | Large overlays and building rubble are partly cut off while they scroll onto the screen. | They are drawn complete. | always on |
| CT1-0384 | Graphics & UI | Drop pods use the infantry's own palette instead of the house colours (mods). | The pod is drawn in the house colours. | always on |
| CT1-0390 | Graphics & UI | An Aircraft Carrier that has launched all its Hornets still casts the shadow of a loaded carrier (NoSpawnAlt=yes). | The shadow matches the empty deck. | always on |
| CT1-0405 | Graphics & UI | An area once seen through a destroyed Gap Generator's cover can never be hidden again by another Gap Generator. | The other generator hides it again. | always on |

### Other (51)

| ID | Category | Before (original game) | After (patched) | Switch |
|---|---|---|---|---|
| CT1-0001 | Other | Campaign and network savegames do not store the game options; after loading, options such as BuildOffAlly are whatever the last skirmish setup screen left behind. | The game options are saved and restored with the savegame. (This changes the savegame format.) | always on |
| CT1-0005 | Other | A mind-controlled MCV deployed by its controller becomes the controller's construction yard for good; killing the controller does not give it back. | Default rules make buildings immune to mind control, so the deployed yard is released and returns to its original owner; in mods that allow it, control carries over with a proper link and ends with the controller. | always on |
| CT1-0006 | Other | When an Engineer captures a mind-controlled building, the mind-control link survives, and the building goes back to its old owner when the controller dies. | Capturing breaks the mind-control link; the building stays with the Engineer's owner. | always on |
| CT1-0022 | Other | Random crates appear more often near the edges and corners of the map. | Crates are spread evenly over the visible map area. | always on |
| CT1-0031 | Other | Grinding buildings with UnitAbsorb / InfantryAbsorb still have the wrong kind of unit sent to them; those units walk there and stand forever. | Units that the building does not accept are not sent there and go idle. | always on |
| CT1-0033 | Other | Engineers can walk into an ally's Grinder even at full health (the ally gets the money). | Engineers can no longer enter an ally's Grinder at full health. | always on |
| CT1-0046 | Other | When a transport carrying another loaded transport is destroyed, the kills of the inner passengers are credited to the wrong unit. | All passengers' deaths are credited to the real killer. | always on |
| CT1-0049 | Other | Infantry created by an Ivan bomb explosion (MakeInfantry on its animation) belongs to the neutral side. | It belongs to the bomber's house. | always on |
| CT1-0054 | Other | A computer-owned unit taken over by the player (mind control, hijacking) keeps its old AI order and still walks off to garrison, enter or attack. | The old order is dropped and the unit guards. | always on |
| CT1-0057 | Other | Debris counts are off by one (MaxDebris=1 never makes debris); a short DebrisMaximums list reads garbage, and DebrisMaximums=0,0 freezes the game when the unit dies. | MaxDebris is the real cap, short lists are handled and there is no freeze. | always on |
| CT1-0060 | Other | A non-Construction-Yard building with UndeploysInto= cannot be sold (it undeploys instead), and selling a Construction Yard plays no "structure sold" message. | Such buildings can be sold normally, and the "structure sold" message plays. | always on |
| CT1-0066 | Other | SpySat=yes on a building upgrade does not reveal the map. | The upgrade reveals the map like the Spy Satellite Uplink while powered. | always on |
| CT1-0080 | Other | After loading a savegame, the post-teleport freeze of Chrono units (Chrono Legionnaire, Chrono Miner) is gone, and several other timers and states are reset. | The freeze and the other states survive saving and loading. | always on |
| CT1-0102 | Other | A damaged aircraft on its airfield is not repaired until it has flown and landed again; computer aircraft can stay asleep on the pad. | The airfield repairs and re-arms it in place. | always on |
| CT1-0107 | Other | The Stop command does not fully stop units: moving jumpjets hover on, attack-move and area-guard groups keep fighting, aircraft fly on, units on high bridges ignore it. | Stop works on all of them; returning aircraft keep their landing pad. | always on |
| CT1-0122 | Other | Spawns of a building larger than 1x1 come back and circle over it forever instead of landing and reloading. | They land and reload. | always on |
| CT1-0134 | Other | EnterBioReactorSound, LeaveBioReactorSound and EnterGrinderSound set on the building type are never used. | The building type's sounds are played. | always on |
| CT1-0141 | Other | Buildings that can call airstrikes are always tinted laser red, and a building targeted by an enemy airstrike loses its own airstrike. | Red only while actually targeted; its own airstrike is kept. | always on |
| CT1-0143 | Other | Infantry ignore Passengers and SizeLimit when entering buildings (e.g. 8 infantry all get into a 5-slot Bio Reactor). | Only as many as fit get in; the rest are refused. | always on |
| CT1-0144 | Other | Deploying a single unit with the hotkey or command bar plays no VoiceDeploy. | The deploy voice plays. | always on |
| CT1-0158 | Other | A human player's Spy or Engineer force-fired at a building walks in and infiltrates or captures it. | Force-fire no longer sends it in; a normal click still does. Computer units are unchanged. | always on |
| CT1-0164 | Other | Occupants ordered out of a garrisoned building with no free space around it (or evicted by a trigger or the computer) vanish. | They stay inside when there is no room; when evicted they are put at the building's corner, as when it is sold. | always on |
| CT1-0187 | Other | A building that changes owner during its build-up animation skips it and can keep stale state. | The build-up keeps playing for the new owner. | always on |
| CT1-0190 | Other | When a unit changes owner, effects aimed at it are not cleared: Chrono weapons keep erasing it and spawns keep attacking it although it is now friendly. | Those effects are released when the owner changes. | always on |
| CT1-0198 | Other | When an object leaves the game update list during an update (e.g. a Terror Drone jumping into a vehicle), the next object is skipped for a frame. | Every object is updated once per frame. | always on |
| CT1-0213 | Other | Deploying or undeploying a damaged unit (MCV, Slave Miner) often costs it 1 hit point. | Health is kept exactly. | always on |
| CT1-0216 | Other | A vehicle dropped by a Magnetron plays its crash voice and sound. | It lands silently; shot-down aircraft still play them. | always on |
| CT1-0224 | Other | Infantry going idle while still holding a target (e.g. out of range after a move) go after it again. | They guard or stop as the normal idle rules say. | always on |
| CT1-0232 | Other | Spawns and slaves (e.g. Kirov or Carrier spawns, Slave Miner slaves) caught in a selection obey stop, deploy and scatter orders. | They ignore those orders. | always on |
| CT1-0233 | Other | A unit linked to a building (e.g. a Robot Tank on a Service Depot) keeps the link when it is switched off, blocking the building for others. | The link is dropped. | always on |
| CT1-0235 | Other | Making a factory primary can also make it start unloading if it has occupants or absorbs units (mods). | It only becomes primary. | always on |
| CT1-0236 | Other | Many radiation sites at once (several Desolators, nukes) recompute lighting constantly and the game stutters badly. | Lighting is only recomputed when the radiation level actually changes. | always on |
| CT1-0238 | Other | A building with occupants (e.g. a Bio Reactor) cannot be emptied with the deploy hotkey or command bar button. | The occupants come out. | always on |
| CT1-0240 | Other | In the debug log, computer players' score lines are not named correctly on non-English systems (no visible effect in the normal game). | They are named "Computer". | always on |
| CT1-0275 | Other | Chrono weapons give experience for erasing friendly units, and give double experience for enemy kills. | No experience for friendly kills; enemy kills count once. | always on |
| CT1-0278 | Other | Some redundant work is done at start-up and in cell look-ups (no visible effect). | The redundant calls are skipped; behaviour is unchanged. | always on |
| CT1-0279 | Other | Without PrismSupportModifier in rulesmd.ini, the value is multiplied by 100 every time the rules are read, making Prism support beams far too strong. | The value stays correct. | always on |
| CT1-0286 | Other | With several custom map packages (.PKT files), the maps of the first package are listed again for each further package. | Each map is listed once. | always on |
| CT1-0287 | Other | A tank removed from the map while sitting in a Tank Bunker (erased, deleted, chronoshifted) leaves the bunker locked. | The bunker is freed. | always on |
| CT1-0289 | Other | Pressing G with an enemy unit in the selection stops the guard order at that unit; own units after it get nothing. | All own units guard. | always on |
| CT1-0307 | Other | An Ivan bomb vanishes when the building or vehicle carrying it is sold, or when an MCV carrying it deploys. | The bomb goes off on selling, or moves to the deployed (or packed-up) building. | always on |
| CT1-0311 | Other | Units can be deployed while they are still being chronoshifted in or falling (dropped by a Magnetron). | Deploying waits until they have landed. | always on |
| CT1-0314 | Other | Only multi-turret transports keep their last passenger on unloading; gunner transports without extra turrets let everyone out. | Gunner transports keep their gunner; other transports let everyone out. | always on |
| CT1-0315 | Other | The Stop command briefly interrupts own units that have gone berserk. | Berserk units ignore it. | always on |
| CT1-0316 | Other | Spies can infiltrate allied and own buildings (the ally loses money, radar ...). | They stop instead. | always on |
| CT1-0319 | Other | Ships docked in a Naval Yard for repair cannot be damaged (the war-factory exit protection applies to them). | They take damage normally. | always on |
| CT1-0325 | Other | A Construction Yard sold or undeployed where its MCV cannot be placed can leave an invisible MCV that keeps the player alive. | The MCV is placed, or removed and refunded. | always on |
| CT1-0326 | Other | A building destroyed while being sold lets its survivors out twice. | Survivors come out once. | always on |
| CT1-0345 | Other | Deploying or undeploying a spawner (e.g. a V3 Launcher turned into a building) refills its spawns at once. | The spawns regenerate at SpawnRegenRate. | always on |
| CT1-0374 | Other | The Spy Satellite keeps the map revealed during a power outage, and a refinery frozen by a Chrono weapon still gives its bonus. | The map is shrouded again without power, and the frozen refinery gives no bonus. | always on |
| CT1-0378 | Other | Sounds made of attack / body / decay samples with different formats play the later parts at the wrong speed or as noise (mod sounds; stock sounds are not affected). | Each part plays at its own format. | always on |
