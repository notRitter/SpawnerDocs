---
title: Welcome
nav_order: 1
---

# Ritter Ultimate Event Spawner Documentation
<img src="https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/RitterUltimateEventSpawnerLogo.png" alt="Ritter Ultimate Event Spawner Logo" style="width: 25%; height: auto;"/>

Welcome! This is the official documentation for the **Ritter Ultimate Event Spawner** (RPG Maker MV & MZ).

---

## Quick Links

* **Itch.io Store Page:** [Ritter Ultimate Event Spawner on Itch.io](https://notritter.itch.io/ritter-ultimate-event-spawner-rpg-maker-mv)

---

## What this plugin does

The **Ritter Ultimate Event Spawner** simplifies and supercharges dynamic content for RPG Maker:

* Spawn events by [coordinates](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#spawn-event), [region](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#spawn-event-by-region), or [terrain tag](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#spawn-event-by-terrain-tag)
* [Transform existing events](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html#spawn-event-by-terrain-tag) into live instances of template events
* Save events for persistent gameplay or use temporary spawns
* Recycle event objects to prevent memory bloat and preserve performance
* [Boundary-based spawning system](https://notritter.github.io/SpawnerDocs/Guides/Boundary/introduction.html) for streaming large maps
* [Full script-call (MV&MZ)](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html) and [plugin-command control(MZ)](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html)

---

## Documentation Structure

Below is a concise map of the docs so you can land on the exact topic you need.

### Base Spawner

**Documentation**

* [Installation & Setup](https://notritter.github.io/SpawnerDocs/Documentation/installation.html)
* [Script Calls](https://notritter.github.io/SpawnerDocs/Documentation/scriptcalls.html)
* [Plugin Commands (MZ)](https://notritter.github.io/SpawnerDocs/Documentation/plugincommands.html)
* [Event Recycling (how recycling works)](https://notritter.github.io/SpawnerDocs/Guides/eventrecycling.html)
* Spawn Maps & Templates
* Saved Events System
* Event Transformations

**Guides**

* Beginner Quick Start
* Performance Optimization
* Random Encounter Setup
* Dynamic Objects & World Population

---

### Boundary Spawner

**Documentation**

* [Boundary System Introduction](https://notritter.github.io/SpawnerDocs/Guides/Boundary/introduction.html)
* [Boundary Script Calls](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/scriptcalls.html)
* [Boundary Plugin Commands (MZ)](https://notritter.github.io/SpawnerDocs/Documentation/Boundary/plugincommands.html)
* Boundary Types & Behaviors
* Auto Handlers & Update Methods
* Saved Boundary Events & Preloading

**Guides**

* Event Streaming Quick Start Guide!
* Correctly designing Spawn & Unspawn Zones
* [Boundary System Best Practices](https://notritter.github.io/SpawnerDocs/Guides/Boundary/bestpractices.html)
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

If you find a problem or want to suggest improvements, please make a post on the forums on store page for the plugin on Itch.io.

---
