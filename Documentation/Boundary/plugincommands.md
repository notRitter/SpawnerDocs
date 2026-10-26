---
title: Plugin Commands
parent: Boundary Documentation
nav_order: 2
---

# Boundary System Plugin Commands Documentation
MZ Only
{: .label .label-blue }

These commands appear under **Ritter_EventSpawner** in MZ's Plugin Command interface and require BoundarySystem above EventSpawner. MV uses the [script calls](scriptcalls.html), plus the template metadata form below.

Screenshots illustrate the workflow; the exact argument names below match the current source. IDs and coordinates must be valid for your project. Commands run synchronously: successful output IDs are available to the next event command.

#### TABLE OF CONTENTS

* [Create Boundary](#create-boundary)
* [Add Auto Handler](#add-auto-handler)
* [Boundary Event Setup](#boundary-event-setup)
* [Init Boundary Events](#init-boundary-events)
* [Activate Boundary](#activate-boundary)
* [Deactivate Boundary](#deactivate-boundary)
* [Enable Event](#enable-event--disable-event)
* [Disable Event](#enable-event--disable-event)
* [Edit Spawner Boundary](#edit-spawner-boundary)
* [Preload Saved Boundary Event](#preload-saved-boundary-event)
* [Preload Saved Boundary Event (Region)](#preload-saved-boundary-event-region)

---

## Create Boundary

![Create Boundary](../../Assets/BoundarySpawner/PluginCommands/CreateBoundary_Plugin_Command.png)

Command ID: `CreateBoundary`.

| Argument key | Purpose |
| --- | --- |
| `Name` | Unique owning-boundary name. Keep it stable once persistent entities exist. |
| `Width`, `Height`, `Thickness` | Tile dimensions and outward edge thickness. |
| `EventId` | `0`: player; positive runtime ID: event; `-1`: static center. |
| `ExpandBy` | Extra player-direction expansion when applicable. |
| `MaxEvents` | Positive live capacity for a spawn boundary; `0` is not unlimited. |
| `CenterX`, `CenterY` | Used for static centers. |

Configure an auto handler next to establish the boundary's map/type settings. A disabled handler can be used for manual control. The command editor defaults to player anchor `EventId = 0`, `ExpandBy = 1`, and `MaxEvents = 30`; the script defaults are `-1`, `0`, and `0` respectively. Set geometry, anchor, expansion, and capacity deliberately.

## Add Auto Handler

![Add Auto Handler](../../Assets/BoundarySpawner/PluginCommands/AddAutoHandler_Plugin_Command.png)

Command ID: `AddAutoHandler`.

| Argument key | Purpose |
| --- | --- |
| `Name` | Existing boundary name. |
| `SpawnMap` | Numeric source template map ID for spawn handlers. Unspawn-only handlers need no source templates; `0` is sufficient. |
| `Maps` | Gameplay map ID list. This is separate from the source map. |
| `Type` | `SpawnOn`, `SpawnIn`, `FillOn`, `FillIn`, `UnspawnOn`, or `UnspawnIn`. |
| `UpdateMode` | `WaitTime` or `Movement`. |
| `Wait` | Frame interval for WaitTime mode; not a Movement cooldown. |
| `Enabled` | Initial activation on configured maps. |
| `UnspawnSettings` | For an unspawn handler, enable **Unspawn** and set **Boundaries** to the exact owning spawn-boundary names it may remove events from. |

The editor serializes `UnspawnSettings` as a struct with `Unspawn` and `Boundaries` fields; `Boundaries` is a JSON-encoded string array, such as `["NPCs", "Resources"]`. Use the flat list in the editor. See [boundary types](boundarytypes.html) for footprint and saved-entry processing.

## Boundary Event Setup

![Boundary Event Setup](../../Assets/BoundarySpawner/PluginCommands/BoundaryEventSetup_Plugin_Command.png)

Command ID: `BoundaryEventSetup`. Place it on **page 1 of a template event** for automatic generation of new events. It is scanned as metadata, not executed as a spawn request. It is not required just to create an explicit preload.

| Argument key | Purpose |
| --- | --- |
| `BoundaryList` | Owning spawn-boundary names permitted to generate this template. |
| `MapList` | Gameplay map IDs on which generation is allowed. |
| `RegionsList` | Eligible region IDs. |
| `Enabled` | Initial fresh-spawn eligibility. |
| `Save` | Whether newly generated instances are saved boundary entities. |

### RPG Maker MV metadata

Place this MV Plugin Command (event command 356) on page 1 of the template:

```text
Ritter_EventSpawner BoundaryEventSetup {"BoundaryList":["NPCs"],"MapList":[1],"RegionsList":[1,3],"Enabled":true,"Save":true}
```

These fields also accept the JSON-encoded array strings and `"true"`/`"false"` values produced by MZ. This metadata form does not make MZ's full command interface available in MV.

Saved boundary entities keep their BEID while runtime event IDs can change. See [supported persistent state](savedboundaryevents.html); arbitrary external plugin caches are not automatically preserved.

## Init Boundary Events

![Init Boundary Events](../../Assets/BoundarySpawner/PluginCommands/Init_Boundary_Plugin_Command.png)

Command ID: `InitializeBoundary`; argument: `Name`.

For an **active spawn boundary**, processes stored saved/preloaded entries throughout its interior up to capacity. This can populate the interior initially even when later streaming uses SpawnOn/FillOn. It does not generate a fresh event on every empty tile or instantiate every record in the world.

Use after initial population setup if immediate interior restoration is wanted. Previously live persistent events restore automatically on game load; no movement or repeated Initialize commands are needed for that restoration. Inactive boundaries are not initialized by this command.

## Activate Boundary

![Activate Boundary](../../Assets/BoundarySpawner/PluginCommands/ActivateBoundary_Plugin_Command.png)

Command ID: `ActivateBoundary`; argument: `Name`.

Enables processing on configured maps. Automatic processing still checks geometry, stored records/template rules, and capacity. Activation alone does not instantiate every dormant entry.

## Deactivate Boundary

![Deactivate Boundary](../../Assets/BoundarySpawner/PluginCommands/Deactivate_Boundary_Plugin_Command.png)

Command ID: `DeactivateBoundary`; arguments: `Name`, `UnspawnAll`.

Stops updates. With `UnspawnAll: false`, live events stay in place. With `true`, owned live events unspawn: saved state remains available; temporary entities expire.

## Enable Event / Disable Event

![Enable Event](../../Assets/BoundarySpawner/PluginCommands/EnableEvent_Plugin_Command.png)
![Disable Event](../../Assets/BoundarySpawner/PluginCommands/DisableEvent_Plugin_Command.png)

Command IDs: `EnableEvent`, `DisableEvent`; arguments: `SpawnMap`, `EventId`.

Changes a template's eligibility for **new generated** auto-boundary spawns. Existing saved clones and preloads are not deleted or disabled. These are not persistent quest-state setters or controls for one BEID.

## Edit Spawner Boundary

![Edit Boundary](../../Assets/BoundarySpawner/PluginCommands/EditBoundary_Plugin_Command.png)

Command ID: `EditBoundary`; arguments: `Name`, optional `Width`, `Height`, `Thickness`, `ExpandBy`, `X`, `Y`, `MaxEvents`, `WaitTime`.

Leave an optional field empty to retain its current value. Zero is a real edit. `X`/`Y` change a static center. Editing geometry invalidates its tile cache; it does not promise immediate removal of events now outside the new footprint. Check the paired unspawn coverage after editing.

## Preload Saved Boundary Event

![Preload Event](../../Assets/BoundarySpawner/PluginCommands/PreloadEvent_Plugin_Command.png)

Command ID: `PreloadEvent`; arguments: `BoundaryName`, `SpawnMap`, `SpawnEventId`, `XPosition`, `YPosition`, `varBEID`, `varType`.

Creates one persistent metadata record on the current gameplay map without creating a live event. The boundary/map saved-event tile must be empty. On success its BEID is written synchronously to **BEID -> Game Variable** (`varBEID`); `0` disables output.

Setter Type (`varType`) is `SetValueAsInteger` or `PushValueAsArray`. Array mode initializes an array if necessary. Failed creation does not supply a new identity; do not treat a previously stored output as a new success.

For example, configure `BoundaryName = Static Spawn`, `SpawnMap = 2`, `SpawnEventId = 1`, `XPosition = 8`, `YPosition = 10`, `varBEID = 1`, and `varType = SetValueAsInteger`. On success, `$gameVariables.value(1)` is the persistent BEID, not a runtime event ID. Retain the boundary name and owning gameplay map ID too. Template IDs and coordinates must be valid; preload creation is not full template or passability validation.

## Preload Saved Boundary Event (Region)

![Preload Event Region](../../Assets/BoundarySpawner/PluginCommands/PreloadEventRegion_Plugin_Command.png)

Command ID: `PreloadEventRegion`; arguments: `BoundaryName`, `SpawnMap`, `SpawnEventId`, `Regions`, `varBEID`, `varType`.

Creates **one** preload at a randomly selected matching region tile across the current map, including outside the boundary's current footprint. `Regions` is the editor's region-ID array. Output handling matches PreloadEvent.

Use the same boundary/template/output settings as the coordinate example, replacing `XPosition`/`YPosition` with `Regions = [1]`. Paint region 1 on the current gameplay map. If no candidate is available or insertion fails, the command logs a warning and leaves the output variable unchanged. Invalid Regions JSON is logged and becomes an empty list, so it cannot create a preload.

The name-based wrappers, these MZ commands, BEIDs, and self-variable transfer are new in v2.1. Basic preloading already existed in v2.0a. See [preload script examples](scriptcalls.html#9-preloading-and-beid-control) for capturing entries and using BEIDs, and [Preloaded Boundary Events](preloadevents.html) for eligibility and first-spawn behavior.
