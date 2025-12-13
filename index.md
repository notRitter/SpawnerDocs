---
title: Welcome
nav_order: 1
---

# Ritter Ultimate Event Spawner Documentation
<img src="https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/RitterUltimateEventSpawnerLogo.png" alt="Ritter Ultimate Event Spawner Logo" style="width: 25%; height: auto;"/>

Welcome!

This is the official documentation for the **Ritter Ultimate Event Spawner** (RPG Maker MV & MZ).

---

## Quick Links

* **Store Page:** [Ritter Ultimate Event Spawner on Itch.io](https://notritter.itch.io/ritter-ultimate-event-spawner-rpg-maker-mv)
* **Store Page:** [Ritter Boundary System on Itch.io](https://notritter.itch.io/ritter-boundary-system-rpg-maker-mz)

---

## What this plugin does

The **Ritter Ultimate Event Spawner** simplifies and supercharges dynamic content for RPG Maker:

* Spawn events by [coordinates](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#spawn-event), [region](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#spawn-event-by-region), or [terrain tag](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#spawn-event-by-terrain-tag)
* [Transform existing events](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#transform-event) into live instances of [template events](https://notritter.github.io/SpawnerDocs/Documentation/installation.html#4-create-a-template-map-for-source-events)
* [Save events](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#examples) for persistence throughout gameplay or use temporary spawns
* [Recycle event objects](https://notritter.github.io/SpawnerDocs/Guides/eventrecycling.html) to prevent memory bloat and preserve performance
* Full [script-call (MV&MZ)](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html) and [plugin-command (MZ)](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html) control
* [Boundary-based spawning system](https://notritter.github.io/SpawnerDocs/Guides/Boundary/introduction.html) for streaming events on large maps

---

## Documentation Structure

Below is a concise map of the docs so you can land on the exact topic you need.

### Base Spawner

**Documentation**

* [Installation & Setup](https://notritter.github.io/SpawnerDocs/Documentation/installation.html#installation)
* [Script Calls](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#script-calls)
* [Plugin Commands (MZ)](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html#plugin-commands)
* [Spawn Maps & Templates](https://notritter.github.io/SpawnerDocs/Documentation/spawnmapsandtemplates.html)
* [Saved Events](https://notritter.github.io/SpawnerDocs/Documentation/savedevents.html)

**Guides**

* [Event Recycling (how recycling works)](https://notritter.github.io/SpawnerDocs/Guides/eventrecycling.html)
* Beginner Quick Start
* Performance Optimization

---

### Boundary Spawner
Requires Ritter Boundary System


**Documentation**

* [Boundary System Introduction](https://notritter.github.io/SpawnerDocs/Guides/Boundary/introduction.html#boundary-system-introduction)
* [Boundary Script Calls](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/scriptcalls.html#boundary-system--script-call-documentation)
* [Boundary Plugin Commands (MZ)](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/plugincommands.html#boundary-system-plugin-commands-documentation)
* Boundary Types & Behaviors
* Auto Handlers & Update Methods
* [Saved Boundary Events](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/savedboundaryevents.html)
* [Preloading Saved Boundary Events](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/preloadevents.html)
* [BEID Database](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/beiddatabase.html)

**Guides**

* Event Streaming Quick Start Guide!
* Correctly designing Spawn & Unspawn Zones
* [Boundary System Best Practices](https://notritter.github.io/SpawnerDocs/Guides/Boundary/bestpractices.html#boundary-system-best-practices-guide)
* Expanding Worlds with Boundaries
* Boundary Debugging & Visualizer Usage

---

## Recommended starting points

* New users: **Beginner Quick Start**
* Want best performance: **Event Recycling** + **Performance Optimization**
* Try event streaming: **Event Streaming Quick Start Guide!**

---

## Notes for MV vs MZ users

* **MV:** Script calls are the sole integration point.
* **MZ:** Full plugin-command support via the editor UI; examples in the Plugin Commands pages show MZ UI screenshots and arguments.

---

## Contribute / Report issues

If you find a problem or want to suggest improvements, please make a post on the [forums](https://notritter.itch.io/ritter-ultimate-event-spawner-rpg-maker-mv/community) which can be found on the [store page on Itch.io.](https://notritter.itch.io/ritter-ultimate-event-spawner-rpg-maker-mv)

---
