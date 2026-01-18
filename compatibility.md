---
title: Compatibility Guide
nav_order: 6
---

# Plugin Compatibility Guide

### For Developers Integrating with **Ritter Ultimate Event Spawner**

> This guide explains **how to make RPG Maker MZ plugins compatible** with Ritter Ultimate Event Spawner systems.
>
> If your plugin interacts with **events, characters, or sprites**, this document is for you.

---

## Why This Guide Exists

Ritter Ultimate Event Spawner allows events to be:

* Spawned dynamically
* Erased at runtime
* Restored from saved data
* Recycled multiple times during gameplay

This breaks a **common assumption** many plugins make:

> *“Events are created once and live forever.”*

If your plugin attaches **sprites, data, or cached references** to events without proper cleanup, those objects may persist after the event is erased—causing:

* Frame drops
* Sprite duplication
* Garbage collection spikes
* Performance degradation over time

This guide shows the **correct lifecycle hooks** to use so your plugin remains safe, efficient, and compatible.

---

## The One Rule That Solves Most Issues

### ✅ **Always clean up plugin state when an event is erased**

Ritter Event Spawner (and most spawners) remove events using:

```js
Game_Event.prototype.erase()
```

If your plugin cleans up *everything it added* during `erase()`, it will automatically support:

* Event recycling
* Respawning
* Streaming / boundary systems
* Large dynamic maps

---

## What Needs Cleanup?

If your plugin does **any** of the following, cleanup is required:

* Adds sprites to events (icons, text, overlays, filters, effects)
* Tracks events in arrays, sets, or maps
* Caches data by `eventId`
* Attaches custom properties to `Game_Event` or `Sprite_Character`
* Creates bitmaps dynamically
* Uses per-frame scanning of `$gameMap.events()`

---

## Recommended Lifecycle Hooks

| Purpose                | Hook                            |
| ---------------------- | ------------------------------- |
| Initialize plugin data | `Game_Event.initialize`         |
| Clean up everything    | `Game_Event.erase`              |
| Handle sprite reuse    | `Sprite_Character.setCharacter` |

---

## Copy-Paste Compatibility Template

This template can be adapted for **any plugin** that interacts with events.

---

### 1️⃣ Initialize plugin fields safely

Use **non-enumerable properties** to avoid save bloat and serialization issues.

```js
(() => {
  const _Game_Event_initialize = Game_Event.prototype.initialize;
  Game_Event.prototype.initialize = function(mapId, eventId) {
    _Game_Event_initialize.call(this, mapId, eventId);

    Object.defineProperties(this, {
      _myPluginSprite: { value: null, writable: true, configurable: true },
      _myPluginData:   { value: null, writable: true, configurable: true },
      _myPluginKey:    { value: null, writable: true, configurable: true }
    });

    // Optional: register event if it uses your plugin feature
    // if (this.event()?.meta?.MyPluginTag) MyPlugin.register(this);
  };
})();
```

---

### 2️⃣ Clean up on `erase()` (critical)

This is the **most important step**.

```js
(() => {
  const _Game_Event_erase = Game_Event.prototype.erase;
  Game_Event.prototype.erase = function() {
    try {
      // Remove attached sprites
      if (this._myPluginSprite) {
        this._myPluginSprite.parent?.removeChild(this._myPluginSprite);
        this._myPluginSprite.destroy?.({
          children: true,
          texture: false,
          baseTexture: false
        });
        this._myPluginSprite = null;
      }

      // Remove from registries / caches
      // MyPlugin.unregister(this);

      // Clear cached references
      this._myPluginData = null;
      this._myPluginKey = null;

    } catch (e) {
      console.warn("MyPlugin cleanup error:", e);
    }

    _Game_Event_erase.call(this);
  };
})();
```

✔ Ensures no orphaned sprites
✔ Prevents memory leaks
✔ Safe for recycling and respawning

---

### 3️⃣ Handle sprite reuse (`Sprite_Character.setCharacter`)

Some systems reuse character sprites internally.
Clean any plugin attachments when rebinding.

```js
(() => {
  const _Sprite_Character_setCharacter =
    Sprite_Character.prototype.setCharacter;

  Sprite_Character.prototype.setCharacter = function(character) {
    // Cleanup old attachments
    if (this._myPluginChild) {
      this._myPluginChild.parent?.removeChild(this._myPluginChild);
      this._myPluginChild = null;
    }

    _Sprite_Character_setCharacter.call(this, character);

    // Attach new plugin visuals if needed
    // if (character && character._myPluginFlag) { ... }
  };
})();
```

---

## What to Avoid (Very Important)

❌ **Avoid per-frame scanning**

```js
// Bad
$gameMap.events().forEach(event => { ... });
```

✅ **Register once, update only tracked events**

---

❌ **Avoid permanent caches by eventId**

```js
cache[event.eventId()] = data;
```

✅ **Clear caches on erase or rebind**

---

❌ **Avoid requestAnimationFrame loops**

```js
requestAnimationFrame(updateLoop);
```

✅ **Use RPG Maker’s `update()` lifecycle**

---

❌ **Avoid leaving sprites in containers**

```js
// Bad: sprite stays forever
container.addChild(sprite);
```

✅ **Always remove on erase**

```js
container.removeChild(sprite);
```

---

## Optional: Spawner-Aware Optimization

You may optionally detect Ritter Event Spawner and switch to lifecycle-based logic:

```js
if (Imported?.Ritter_EventSpawner) {
  // Use erase / initialize hooks instead of scanning
}
```

This is **not required**, but improves performance in large projects.

---

## Why This Matters

Without proper cleanup:

* Sprites can outlive events
* Garbage builds up silently
* GC spikes cause stutter
* Performance degrades over time
* Issues appear only after long play sessions

With proper cleanup:

* Your plugin becomes recycling-safe
* Works with large dynamic maps
* Compatible with spawners & streamers
* Scales cleanly to thousands of events

---

## Summary (10-Second Checklist)

✔ Initialize plugin data on `Game_Event.initialize`
✔ Clean **everything** on `Game_Event.erase`
✔ Remove sprites from parents
✔ Clear references and caches
✔ Avoid per-frame global scans

That’s it.
