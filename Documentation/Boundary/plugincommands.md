---
title: Plugin Commands
parent: Boundary Documentation
nav_order: 2
---

# Boundary System Plugin Commands Documentation

This page documents **the Plugin Commands** for the Ritter Ultimate Event Spawner’s **Boundary System**.
For MZ users, these appear in the Plugin Command interface.

---

# Create Boundary

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/CreateBoundary_Plugin_Command.png)

Creates a Boundary used for spawning or unspawning events.

### Parameters

* **width** — Number of tiles wide the boundary is.
* **height** — Number of tiles high the boundary is.
* **thickness** — Number of tiles thick the boundary wall is.
* **name** — Unique boundary name used later when spawning or unspawning.
* **eventId** — Determines boundary anchor:

  * `0` = Boundary follows the **player**
  * `>0` = Boundary follows **event with that eventId**
  * `-1` = Boundary is centered on **x,y coordinates** — requires `centerx` and `centery`
* **expandBy** — Extra tiles extending in the player’s movement direction to encourage forward-facing spawns.
* **maxEvents** — Max number of events this boundary may actively manage.
* **centerx** — Only used when `eventId = -1`. Center X position.
* **centery** — Only used when `eventId = -1`. Center Y position.

---

# Add Auto Handler

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/AddAutoHandler_Plugin_Command.png)

Enables automatic spawning/unspawning for a boundary.

### Parameters

* **name** — Boundary name.
* **spawnMap** — ID of the SpawnMap to pull events from.
* **type** — Mode of operation:

  * `SpawnOn` — Spawn events on boundary edge
  * `SpawnIn` — Spawn events inside boundary
  * `FillOn` — Fill every valid tile on boundary edge
  * `FillIn` — Fill every valid tile inside boundary
  * `UnspawnOn` — Unspawn events on boundary edge
  * `UnspawnIn` — Unspawn events inside boundary
* **updateMode** — How the boundary updates:

  * **Wait Time** — Update every *n* frames
  * **Player Move** — Update each time player moves to a new tile
* **wait** — Number of frames between spawn/unspawn checks.
* **enabled** — Whether the boundary auto‑handler begins active.
* **boundary** — *(Unspawn only)* Array of boundary names it may remove events from. Each unspawned event must match one of the listed boundaries via its `parentBoundary`.

---

# Boundary Event Setup

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/BoundaryEventSetup_Plugin_Command.png)

**Optional / Legacy method.**
This plugin command is placed on **page 1** of SpawnMap events and behaves like metadata.

### Parameters

* **Boundary List** — Array of boundary names this event may spawn on.
* **Map List** — Game map IDs this event is allowed to spawn on.
* **RegionId List** — Region IDs the event may spawn on.
* **Enabled By Default?** — Whether the event is initially allowed to spawn.
* **Saved Event?** — Whether this event should be restored if re‑encountered.

### Important Note About Saved Boundary Events

Boundary‑spawned saved events **do not retain their eventId** when recycled.
This prevents mass ID buildup and performance degradation. All other state persists normally.

---

# Init Boundary Events

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/Init_Boundary_Plugin_Command.png)

### Parameters

* **boundaryName** — Name of the boundary to initialize.

Use this when you want to restore all saved boundary events when loading a map.

Required for:

* `SpawnOn`
* `FillOn`

Not required for:

* `SpawnIn`
* `FillIn`

Use when entering a map or when manually preloading saved boundary events.

---

# Activate Boundary

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/ActivateBoundary_Plugin_Command.png)

Turns a boundary **on**.

### Parameters

* **boundaryName** — Boundary to activate.

A boundary will spawn or unspawn events only when active **and** all other conditions are met:

* Player is on a valid map
* A valid region is within/on the boundary
* Enabled spawn events are available

---

# Deactivate Boundary

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/Deactivate_Boundary_Plugin_Command.png)

Turns a boundary **off**.

### Parameters

* **boundaryName** — Boundary to deactivate.
* **unspawnAll** — `true/false`. Whether to unspawn all events belonging to this boundary immediately.

Once deactivated, the boundary stops all spawn/unspawn activity until activated again.

---

# Enable Event

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/EnableEvent_Plugin_Command.png)

### Parameters

* **spawnMap** — SpawnMap ID.
* **eventId** — Event ID to enable.

Allows this event to be spawned by auto boundaries.

---

# Disable Event

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/DisableEvent_Plugin_Command.png)

### Parameters

* **spawnMap** — SpawnMap ID.
* **eventId** — Event ID to disable.

Prevents the event from being spawned by boundaries.
**Saved versions will still restore** — this only blocks *new* spawns.

---

# Edit Spawner Boundary

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/EditBoundary_Plugin_Command.png)

Modifies an existing boundary’s properties.

### Parameters

* **boundaryName** — Boundary to modify.
* **param** — Property to edit.
* **value** — Value to apply.

Leave unchanged parameters empty.

### Parameter Keys

* `width`
* `height`
* `thickness`
* `expandby`
* `centerx`
* `centery`
* `maxevents`
* `waittime`

### Warning

Changing boundary size may leave some events outside an unspawn boundary. Shrinking boundaries should be done carefully.

---

# v2.1 — NEW FEATURE

**Preload Saved Boundary Events**

Preloading stores a metadata object in the boundary's Saved Event Database.
When the boundary reaches that tile:

* All saved boundary events on the tile which are tied to the boundary are restored
* **Any preload object found on the tile is spawned as a new saved boundary event**
* The preload object is deleted and replaced with the real Game_Event

---

# Preload Saved Boundary Event

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/PreloadEvent_Plugin_Command.png)

### Parameters

* **boundaryName** — Boundary that will spawn the event.
* **spawnMap** — SpawnMap ID.
* **eventId** — Template event ID.
* **x**, **y** — Coordinates to preload.
* **BEID -> Game Variable** — Variable to store the BEID.
* **Setter Type**:

  * `SetValueAsInteger` — Store BEID as number
  * `PushValueAsArray` — Push BEID into an array

Allows deferred spawning until the player is nearby.

---

# Preload Saved Boundary Event (Region)

![Plugin Command UI](https://raw.githubusercontent.com/notRitter/SpawnerDocs/refs/heads/main/Assets/BoundarySpawner/PluginCommands/PreloadEventRegion_Plugin_Command.png)

### Parameters

* **boundaryName** — Target boundary.
* **spawnMap** — SpawnMap ID.
* **eventId** — Template event ID.
* **regions** — Array of region IDs; one is chosen randomly.
* **BEID -> Game Variable** — Variable for BEID.
* **Setter Type** — Same as above.

Preloads an event into a **random region tile** inside the boundary, spawning later when conditions are met.

---
