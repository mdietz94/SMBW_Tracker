# Access-logic generation

The PopTracker access logic in this pack is **generated** from the SMBW
Archipelago apworld, which is the single source of truth for what gates what.
Do not hand-edit the generated outputs; re-run the generator instead.

## Generated outputs

| File | Contents |
|---|---|
| `scripts/logic/smbw_generated_logic.lua` | One Lua function per apworld region implementing AP reachability (`full(R) = R.requires AND OR(full(parents))`), one rule per AP location id, and the `smbw_loc(id)` / `smbw_royal(n)` helpers. Covers **both standard and open-world** modes, switched at runtime off `SLOT_DATA` (open_world / open_world_active / palaces_required). |
| `items/logic_item.json` | Hidden toggle items for the button / Wonder-Effect / Wonder-Flower / lowercase power-up codes the logic reads. These were already mapped in `scripts/autotracking/item_mapping.lua` but had no item object, so `ProviderCountForCode` returned 0 forever. |
| `locations/*.json` | Each section gains `"access_rules": ["$smbw_loc|<apid>"]`. |
| `scripts/init.lua`, `scripts/items_import.lua` | `require` / `AddItems` lines for the two new files. |

## How the two repos line up

The bridge is the Archipelago id, computed identically on both sides:

```
location id = starting_index + 500 + (index in apworld locations.json)
item id     = starting_index +       (index in apworld items.json)
```

which is exactly how this pack's `autotracking/{location,item}_mapping.lua` key
their tables — so paths and codes line up 1:1 with no manual matching.

## Regenerating

```bash
pip install json5
python tools/generate_logic_from_apworld.py <path-to-this-pack> <path-to-apworld>/apworld/smbw_archipelago/data
```

The apworld lives at https://github.com/mdietz94/smbw_ap (`apworld/smbw_archipelago/data/{regions,locations,items}.json`).
