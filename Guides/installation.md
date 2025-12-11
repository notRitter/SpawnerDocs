---
title: Installation
parent: Guides
nav_order: 1
---

# Installation

Follow the steps below to install the **Ritter Ultimate Event Spawner** in your RPG Maker MV/MZ project.  
This guide walks you through downloading, enabling, configuring, and testing the plugin.

---

## 1. Download the Plugin

Download the plugin file and place it inside your project’s:

/js/plugins/

![Download the Plugin](../Assets/BaseSpawner/Installation/Spawner_Install_Folder.png)

---

## 2. Enable the Plugin in Plugin Manager

Open your project’s **Plugin Manager** and add the following:

- `Ritter_EventSpawner`
- `Ritter_BoundarySystem` *(optional, but required if you want to use Boundaries)*

### **Plugin Load Order**

Ritter_BoundarySystem
Ritter_EventSpawner


![Plugin Manager](../assets/images/installation/plugin-manager.png)

---

## 3. Configure Plugin Parameters

After enabling the plugin, adjust its parameters to match your project’s needs.

Key configuration areas include:

- Spawn Map settings
- Spawn Event Starting EventId Number
- Boundary integration settings
- Debug / logging preferences

![Plugin Parameters](../assets/images/installation/plugin-parameters.png)

---

## 4. Create a Template Map for Source Events

The spawner works by copying “template events” from a dedicated map into gameplay maps.

To set this up:

1. Create a **new map** used only for storing template events  
2. Add events you want the spawner to generate during gameplay  
3. These events should **never** appear directly in your game  
4. The spawner duplicates them dynamically when needed  

This map acts like a library of spawnable event types.

![Template Map](../assets/images/installation/template-map.png)

---

## 5. Playtest and Verify the Plugin

Run a playtest and confirm that the spawner is functioning properly.

Try the following:

- Execute **Plugin Commands** to spawn/unspawn events  
- Test **Script Calls** from the console  
- Verify that template events correctly appear on gameplay maps  
- Confirm boundary behavior (if using the Boundary System)

![Playtest Example](../assets/images/installation/playtest.png)

---

## Next Steps

Once installation works, continue with the more advanced guides:

- 👉 [Script Calls](script-calls.md)  
- 👉 [Plugin Commands](plugin-commands.md)  
- 👉 [Boundaries Overview](boundaries.md)  
- 👉 [Terrain-Based Spawning](terrain-spawning.md)  

These will walk you through every system in the spawner.

---
