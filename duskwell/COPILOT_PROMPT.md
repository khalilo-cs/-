# Duskwell: master prompt for GitHub Copilot (Agent mode)

برومبت واحد جاهز للصق في Copilot. انسخ كل ما بعد الخط الأفقي. كُتب بالإنجليزية لأن Copilot يتبعها بدقة أكبر.
فيه ثلاثة أجزاء: **A** لعبة المتصفح الموجودة، **B** مشروع Unity أصلي جديد، **C** قواعد الترخيص والمستودعات المفحوصة.

---

Act as a senior Unity game developer, C# software architect, 2D Metroidvania designer, JavaScript/HTML5 game
engineer, and technical lead.

PROJECT GOAL
Duskwell (Arabic title: أرض الغسق) is an original 2D Metroidvania inspired by the *mechanics* of Hollow Knight
(tight movement, nail combat, pogo strikes, benches, bosses, abilities that gate an interconnected map). It already
exists as a browser game. I want to keep growing it and also build a real Unity version of it, reusing only code and
assets that are legally permitted, so that I fully control the result.

REPOSITORY (read it first)
  https://github.com/khalilo-cs/-
  branch: main  (use ccr-a7cbb23d-ctab00 if pull request #5 is not merged yet)
  - duskwell/              the browser game (HTML5 canvas, plain JavaScript, no build step)
  - duskwell/README.md, duskwell/CREDITS.md    architecture and licences: read both before anything else
  - duskwell/js/           world.js (rooms), entities.js (player + enemies), bosses.js, mechanics.js,
                           pixel.js (normal-mapped 3D lighting for pixel sprites), audio.js,
                           charms.js + art_charms.js (14 original charms, notches, overcharm, shop);
                           lantern stations (fast travel) live in game.js and world.js
  - duskwell/tests/        Playwright bot tests (charms, charm_world, spells, travel, enemies); run each with node, and extend them
  - duskwell/tools/        generators (music, painted backgrounds, sprites, sound effects)
  - duskwell/unity/Scripts C# equivalents of the main systems (27 scripts): study them, they are the starting point
  - duskwell-android/      WebView APK build (./build-apk.sh)
Run the browser game: serve the duskwell folder (python3 -m http.server) and open index.html.
Keep it working at all times: node duskwell/tools-dump.js must print "world OK (33 rooms)".
This is my own project: use ALL the techniques already in it (finite-state-machine enemies, generator-based bosses,
SmoothDamp camera, depth parallax, lightmap + bloom + fog, normal-mapped pixel lighting, layered music,
recorded sound effects, tool scripts) and improve them.

WORKFLOW
1. Audit the workspace (Unity version, project structure, what runs, what is broken). Do not assume anything about
   a repository before you have opened its LICENSE, README and asset licences.
2. Present a concise plan (8 steps) with the licence and compatibility findings.
3. Implement in small, coherent, tested steps; commit each step; keep the project buildable at every stage.
4. Do not stop after writing a plan. Implement what is possible and report blockers precisely.
5. If something needs my permission, a missing asset, or a Unity Editor action, say exactly what is needed and
   continue with all independent tasks.

==============================================================================================================
PART A: THE BROWSER GAME (duskwell/)
==============================================================================================================
Make it bigger and better in the same style:
  1. More content: new rooms and areas, 5+ new enemy types with finite-state-machine AI (idle / patrol / chase /
     anticipation / attack / recoil), 3+ new bosses with phases and telegraphed attacks, new abilities that gate the map.
  2. Feel: coyote time, jump buffering, variable jump height, hit-stop, screen shake, camera SmoothDamp with room
     bounds, parallax layers by depth.
  3. Visuals: dark hand-inked look, dynamic lighting, fog, bloom, normal-mapped sprite lighting (see pixel.js).
     Pixel art lives in duskwell/art/source (the artist's Aseprite files; rebuild with tools/sprites/build_sprites.py).
  4. Audio: layered music that crossfades per area, recorded sound effects, ambience.
  5. Interface: pause menu, map screen, save/load (localStorage), Arabic + English text (RTL), keyboard, gamepad, touch.
  6. Modern tech where it helps: Web Audio API, OffscreenCanvas / WebGL for lighting if it keeps 60 fps on a phone,
     a service worker so the game works offline, a PWA manifest.

==============================================================================================================
PART B: A NEW ORIGINAL UNITY PROJECT (duskwell/unity-project/)
==============================================================================================================
Create a new, independent Unity 2D Metroidvania project with an original identity, as the Unity version of Duskwell.
Create it in duskwell/unity-project/ (the Unity project root). Leave duskwell/unity/Scripts untouched as reference;
copy and improve scripts into the new project. Do not delete, overwrite, or damage anything that exists, and make a
backup before any significant change.

PHASE 1: AUDIT
1. Inspect the current Unity project (if any) and the repositories listed in PART C, if available locally.
   The audit of the two external Hollow-Knight-style repositories is already done and written in PART C; re-verify it
   quickly (LICENSE file, README, asset folders) instead of repeating it from scratch.
2. Review source code, scripts, scenes, prefabs, assets, packages, input system, animation controllers, dependencies.
3. Determine which components are reusable under their licences.
4. Identify missing, broken, obsolete, or incompatible dependencies.
5. Write a detailed inventory (docs/INVENTORY.md) and explain the purpose of each important system.
6. Compare the Unity version of each reference with the installed version and list compatibility issues.

PHASE 2: THE GAME
The game must include:
- An original player character with unique appearance, animation, backstory, and abilities.
- Responsive 2D movement: running, jumping, double-jumping, dashing, wall jumping, falling.
- Melee combat: attack combos, hit detection, damage, knockback, cooldowns, pogo strike.
- Enemy AI: patrol, detection, chasing, attacking, taking damage, death.
- Original boss encounters with several attack patterns, phases, and health bars.
- Health, damage feedback, death, checkpoints (benches), respawning.
- A resource/energy system for special abilities (the soul / healing / dive spell of Duskwell).
- A world of interconnected regions with unique environments (Duskwell has 9 areas and 33 rooms: port them).
- Exploration, hidden areas, collectibles, unlockable abilities, environmental puzzles.
- A map and player navigation system.
- Save/load that keeps progress, unlocked abilities, collected items, and checkpoints.
- Main menu, pause menu, settings menu, inventory, HUD.
- Original sound effects, music, and an audio manager.
- Keyboard and controller support.

PHASE 3: ARCHITECTURE AND CODE
Clean, modular, maintainable C# and Unity best practices. Folders:
  Assets/_Game/
    Scripts/  Player/ Combat/ Enemies/ Bosses/ World/ Abilities/ UI/ SaveSystem/ Audio/ Core/
    Art/ Animations/ Prefabs/ Scenes/ Materials/ Audio/ ScriptableObjects/ Settings/
- Separate reusable components rather than one large script.
- ScriptableObjects for configurable characters, enemies, weapons, abilities, and items.
- No hard-coded values and no unnecessary dependencies.
- Comments and documentation in English (my existing Arabic comments in duskwell/unity/Scripts may stay Arabic).
- Meaningful names. Robust collision handling, object pooling where appropriate, events for communication.
- No memory leaks, no needless Update loops, no expensive operations in hot paths.
- Compatible with the Unity version installed in the workspace. Error handling and debug logging where useful.

PHASE 4: ORIGINAL ART AND LEVEL DESIGN
1. A distinct art direction that does not reproduce Hollow Knight's characters, environments or visual identity.
   Duskwell's own look: dark hand-inked world, dusk palette, the artist's pixel hero (blue / dark) and husk.
2. Original placeholder sprites and shapes where final art is unavailable.
3. A playable test level with platforms, hazards, enemies, checkpoints, and an exit.
4. Tilemaps and reusable prefabs. Parallax backgrounds (use duskwell/art/bg), lighting, particles, effects.
5. Every art and audio asset must have a clear licence or be created for this project (log it in CREDITS.md).

PHASE 5: EDITOR AND GAMEPLAY SETUP
1. Configure scenes, prefabs, layers, tags, physics, input actions, animation controllers.
2. Connect scripts to GameObjects. Set up the player and test enemies in a playable scene.
3. Create UI prefabs and connect buttons and gameplay indicators.
4. Give precise setup instructions for any step that cannot be automated (for example opening the Editor).
5. Leave no critical reference unassigned and rely on no nonexistent asset.

PHASE 6: TESTING AND DEBUGGING
1. Check C# compilation errors (if Unity is not installed, compile with mono mcs against stub types and say so).
2. Find missing references, broken prefabs, invalid component configurations, missing packages.
3. Test movement, combat, enemy behaviour, abilities, checkpoints, saving.
4. Fix errors you introduce.
5. Never claim the game was tested in Unity unless it was really run and tested there.
6. Keep a list of known issues and unresolved tasks.

PHASE 7: DELIVERABLES
- A complete, organised, editable Unity project with at least one complete playable test level.
- All original scripts, reusable prefabs and ScriptableObjects.
- A list of legally reusable components with licence references.
- A README: installation, Unity version, controls, project structure, setup.
- Lists of implemented features, incomplete features, known issues.
- Instructions for building for Windows.

==============================================================================================================
PART C: LICENCING RULES AND REFERENCE REPOSITORIES (strict)
==============================================================================================================
RULES
  - NEVER use art, music, sounds, text, names, maps or code taken or extracted from Hollow Knight, Hollow Knight:
    Silksong, or from any fan repository that contains their assets. They belong to Team Cherry (music: Christopher
    Larkin). A repository licence covers only the files its author made, never the Hollow Knight files inside it.
  - A repository with no LICENSE file is "all rights reserved" by default, even if it is public. Treat it as
    reference-only: read it to understand an idea, close it, and implement the idea yourself in your own words.
  - Never copy copyrighted game assets, proprietary code, maps, characters, music, sounds, or names without explicit
    written permission. If a licence is missing or unclear, do not use the material.
  - Allowed: (a) things you create, (b) CC0 / public domain, (c) MIT / CC-BY / OFL material with the author credited.
    Before adding anything, read its licence file and record it in duskwell/CREDITS.md
    (author, licence, source URL, which files).
  - Do not fabricate repository contents and do not claim you inspected files you did not open.
  - If you cannot access a repository, tell me exactly what to clone or download.

REFERENCE REPOSITORIES AUDITED ON 2026-10-02 (reference-only: do NOT copy code, art or music)
  1. https://github.com/DDR-1/soul-knight   (named "Soul Knight"; not the commercial game of that name)
     - No LICENSE file. README says only: "Fan-made recreation of Hollow Knight game". Last commit 2022-09-25.
     - Unity 2021.2.8f1, about 1,550 lines of C# in 20 scripts: PlayerController, EnemyController, PatrolController,
       GunnerController, traps (FallingTrap, MovingTrap, UnstablePlatform, Projectile, Deadly, DragPlayer), Switch,
       NextLevel, Menu, HUD, HealthDisplay, GlobalController. 3 levels, 82 MB of assets.
     - It ships Hollow Knight's soundtrack (mp3 files named "Enter Hallownest", "Dung Defender", "False Knight",
       "Mantis Lords", "Fungal Wastes", "Decisive Battle") and Hollow Knight area art (folders Hive / Royal / Ruins
       Images, enemy sprite sheets). All of that belongs to Team Cherry. NEVER use any of it, not even as placeholders.
     - Use: only the general ideas (patrolling and gunner enemies, falling and unstable platforms, switches, level
       exits). Duskwell already has these (crumble, mover, saw, lever, stalactite mechanics): improve them
       with your own code.
  2. https://github.com/Quochung2497/2D-Game-Platformer-Personal-Project
     - No LICENSE file, and the README gives no permission to reuse. Unity 2022.3.24f1, about 150 scripts.
     - It contains a folder of extracted Hollow Knight sprites ("Hollow Knight sprites 1.4.3.2 (Voidheart edition)")
       and a file naming a site that redistributes paid Unity assets. NEVER use its art, audio, or anything in Assets
       except what you re-implement yourself.
     - Use: ideas only (bench and respawn flow, scene transitions, ability unlocks, boss health bar, secret map
       reveal). Re-implement them from scratch in duskwell/unity-project.
  3. https://github.com/DanielDFY/Hollow-Knight-Imitation
     - No LICENSE file at the root (only the Bolt plug-in's own LICENSES.txt). Its art files carry Hollow Knight area
       names (for example ruins_mid_walls_0003_a.png), so treat the art as Team Cherry's. The Bolt visual-scripting
       plug-in inside it is a third-party asset with its own licence: do not copy it.
     - Use: study the player-controller and enemy state-machine architecture only; write your own code.

OPEN-LICENSED MATERIAL YOU MAY USE (check each licence file first and list what you use in CREDITS.md)
  - https://github.com/sparklinlabs/superpowers-asset-packs   CC0: sounds, music, pixel art. Prefer the sound
    effects; its art is bright and cartoonish. Already used in duskwell/tools/audio/build_sfx.py.
  - https://github.com/Tiddybub/2d-assets    claims CC0; each pack has SOURCE.md with author and licence: verify.
  - https://github.com/KoBeWi/Metroidvania-System    MIT; map-system ideas (Godot): keep the MIT notice if you port code.
  - https://github.com/EmberNoGlow/hollow-pilot      MIT code (Godot); its third-party assets have their own licences.
  - Duskwell's own files (the artist's Aseprite sprites in duskwell/art/source, the generated music in
    duskwell/audio/music, painted backgrounds in duskwell/art/bg, scripts in duskwell/unity/Scripts).
    The repository itself has no LICENSE file yet: until the owner adds one, treat Duskwell as the owner's own work
    and do not redistribute it as open source.

GAMEPLAY TO STUDY (watch and play it; never copy or extract its art, music or code)
  - Hollow Knight: https://store.steampowered.com/app/367520/Hollow_Knight/
  - Community wiki, for design ideas only (areas, enemies, bosses, abilities, charms): https://hollowknight.wiki

QUALITY RULES
  - Every new room must be reachable, and every progression item obtainable with the abilities the player has at that
    point. Add or extend a bot test (Playwright for the browser game) that proves it.
  - Every boss must be killable and must not soft-lock the player.
  - After each step run the existing tests and open the game to check that it renders.
  - No placeholders ("TODO", "implement later"). Finish what you start. Keep files small and readable; comment the key
    maths (physics, camera, lighting).
  - Report honestly: what you tested, what you could not test, and what is still unfinished.

The final result must be an original, playable, editable game (the browser game kept working, plus a Unity 2D
Metroidvania project): not a copied repository, not a design document, not a pile of disconnected scripts.

Start now: read duskwell/README.md and duskwell/CREDITS.md, summarise the architecture in 10 lines, list the
audit findings, propose the 8-step plan, then implement step 1.
