# Saga Forge (AppADay 146)

Saga Forge is a single file web app for designing the rules of a classic JRPG and then testing the battles you designed. It is App 146 of AppADay, and the first of four forges that build a robust RPG building system. The app ships empty. Nothing is pre-filled: you define the saga, the ruleset, and every record yourself.

Live: https://augustineiacopelli.github.io/appaday-146-saga-forge/
Portfolio: https://augustineiacopelli.github.io/appaday/

## Where it sits in Days 146 to 151

Each forge owns a slice of the shared ID space and exports a bundle that the others can reference.

1. Day 146, Rules (this app). Owns chr_ abl_ itm_ eqp_ sta_ frm_ fam_ enm_ gmb_ trp_ shp_ wth_ lim_ eps_, the module prefixes mat_ job_ cls_, the Living World schemas rmr_ sdq_ bst_, and the Charter chapters chp_.
2. Day 147, Art. Owns spr_ por_ anm_ sfx_ ico_ mus_.
3. Day 148, World. Owns map_ reg_ npc_ twn_ dgn_.
4. Day 149, Story. Owns flg_ qst_ dlg_ evt_ end_.

Days 150 and 151 build on the bundles those four forges export. Their plan is outside this repository.

## The three stages

1. Charter. Write the premise, magic, villain, starting party, chapters, endings, themes, canon, glossary, specs, and quotas, then pick a ruleset (Saga preset, Classic preset, or Custom). Locking the Charter needs zero errors. Amending it later bumps the version and shows which records are affected.
2. Codex. Generate the record types that the locked ruleset calls for, for example Materia for the Saga preset or Class for the Classic preset. Regenerating after an amendment keeps your records and reports what changed.
3. Rules. Author characters, abilities, items, equipment, enemies, families, gambits, troops, shops, weather, limits, and the Expected Party State. Fight your troops by hand in the Arena, then run the Simulator to see whether the balance matches the targets you set.

## The Arena and the Simulator

The Arena fights one troop at a time on a canvas at the internal resolution from the Charter. Battles replay exactly from a battle code.

The Simulator reads the ENGINE:BATTLE source from the page itself, builds a Web Worker from it, and runs every troop tagged to a chapter many times against that chapter's Expected Party State (or a custom party). If a Worker cannot be created it shows an error and does not run on the main thread. Four charts show win rate against target, turn counts, resource cost, and damage by element. A troop more than 10 points from its target is flagged, with links to the troop, its enemies, and the Arena preloaded with the same party and a seed taken from the run.

## Bundle layers

A project is one JSON bundle with these layers.

1. kit: format, schema version, title, timestamps, content hash, the list of opened namespaces, and forge status.
2. charter: version, lock state, sections, specs, quotas, canon, glossary, ruleset, and amendments.
3. codex: generated record types, prefixes, modules, and the save schema.
4. rules: your records, grouped by prefix.
5. art, world, story: reserved for forges 147, 148, and 149.

The content hash is a SHA 256 over the canonical sorted JSON, excluding the hash field itself.

## Forward and broken references

A reference whose prefix belongs to a namespace that is not opened is forward. It is legal and is recorded with the forge that owes it, for example a character portrait that points at por_ records owed by forge 147. A reference into an opened namespace that lacks the ID is broken, and a broken reference blocks Final export. An enum value outside its declared vocabulary is an error. Module rules and advisory checks are warnings. A Draft export is always allowed.

## Export artifacts

Export offers Draft or Final and downloads three files.

1. slug-bundle.json: the bundle with forge 146 status, the rules namespace opened, and a fresh content hash.
2. engine-battle.js: the exact ENGINE:BATTLE source with a header comment giving the engine version and the bundle hash. It declares one global, ENGINE_BATTLE, and has no dependencies.
3. slug-manifest.json: forge number, bundle hash, Charter version, creation time, every referenced ID, the forward references with the forge that owes each one, the broken list, and record counts by prefix.

## AI features are optional

Every AI button (the Charter interviewer, Draft with Claude, Draft a Set, and Suggest from meteorology) needs an API key saved in Settings. Without a key each AI button shows a one line prompt to add one, and everything else stays fully usable. The key and session name live only in this browser. Claude never mints IDs: drafts reference existing records by the IDs supplied in context, unknown references are flagged in review, and accepted drafts get IDs from the registry.

## Build notes

Plain HTML, CSS, and JavaScript in one index.html, with no frameworks and no build step. The only external resources are Google Fonts and Chart.js from jsdelivr. The source is ASCII only, every localStorage call is wrapped in try and catch, tap targets are at least 44px, and layouts work from 375px up. The top of index.html holds a BUILD LOG comment that records every pass, its public API, and known deferrals.
