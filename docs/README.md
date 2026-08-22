# Gatherer Architecture and Developer Guide

Gatherer is an addon that tracks resources (Herbs, Ore, Treasure, and custom resources) gathered by players on the minimap and world map.

## 1.12 Client Restrictions & API Extensions
This fork of Gatherer is built for the 1.12.1 World of Warcraft client (Vanilla), specifically tailored for the Octo-wow project (Turtle WoW 1.18 base).

### Vanilla API Limitations
Because we are developing for the 1.12 API, we face significant restrictions compared to modern WoW (Retail) or even WotLK (3.3.5):
- **Event Handling**: Many modern combat log and tracking events do not exist. We rely heavily on `CHAT_MSG_SPELL_SELF_BUFF` and `UI_ERROR_MESSAGE` to detect successful gathering attempts and skill requirements.
- **Tooltip Scanning**: Tooltip APIs are extremely primitive. We must parse `GameTooltipTextLeft1` to extract the node's localized name during `SPELLCAST_START`.
- **UI API**: Standard UI frames and scaling behave differently, requiring legacy `Frame` XML definitions.

### Available API Extensions
To mitigate these limitations, our client has access to two powerful API extensions via DLL injection:
1. **[ClassicAPI](https://github.com/brues-code/ClassicAPI)**
   - Backports numerous `C_` namespaces (e.g. `C_Map`, `C_Item`, `C_Container`) and utility functions from newer clients.
   - Provides modern Lua string and table functions, as well as `coroutine` support and `hooksecurefunc`.
2. **[SuperWoW](https://github.com/balakethelock/SuperWoW)**
   - Fixes base client bugs and provides a deeper lua-based API for user interfaces.
   - Expands functionality without requiring server-side proxies.

When building features, we can leverage these extensions instead of relying on slow, manual text parsing when an equivalent modern API call (like `C_Item.GetItemInfo`) is available through ClassicAPI.

## Codebase Structure
- **`Gatherer.lua`**: The core application logic. Hooks into events, tracks current nodes, and manages map markers.
- **`GatherIcons.lua`**: Contains tables for mapping localized item names to icon paths and minimum skill levels (`Gather_IconSet`, `Gather_SkillLevel`).
- **`GathererUI.xml` & `GathererUI.lua`**: The configuration window for Gatherer.
- **`localization.lua`**: Contains English and multi-language translations for item names and chat patterns.
- **`constants.lua`**: Core enums like `Gatherer_EGatherType`.
