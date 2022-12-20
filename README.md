## Rhysh Maps
This is just a small Tiled project, used to generate maps that can be imported as features and chunks into the dungeon crawler game I'm making. It's really not intended to be used by anyone else as the tile maps used are actually part of a separate (non-git because lots of large binary files) graphics project. Still, if I don't document how this works, I'll never remember.

### Map Format
The format for these maps is critical as the code that creates features from the map json isn't that smart. A map must:

1. Have a size of 32x32 tiles. Even if it's a smaller feature it expects that size of a data array.
2. Have three layers, named in order from the bottom, Root, Extra, Extended
    - Root can only use tiles in `rhysh-tilemap`
    - Extra can only use tiles in `rhysh-extra`
    - Extended can only use tiles in `kenny-bw-small`
3. Tiles in these maps should have their properties set in such a way that the parser knows what to do with them.
4. Every tile in the Extra or Extended sets should at least have a `type` property set. While the tile properties in the Extra tileset remain consistent in each map, in the Extended tileset these types may map to different values in each map. For instance, there's an extended tile with a type `Event-A` that shows an A on the map. In that particular map it needs to have the value `EventTrigger:some-event` so we create a property that sets `"Event-A" = "EventTrigger:some-event"` so that we know what to do with it in the feature builder.
5. When complete both the maps and the tilesets will need to be exported as JSON and added to the Rhysh project. The tilemaps especially will evolve over time so I expect they will need to be frequently overwritten. When that happens we need to make sure all the old maps have still work with the new tilesets, potentially reexporting them as well.
6. Features also need to have a `FeatureName` and a `FeatureType` property. The name is used to index the feature in the library. The type governs how the feature can be used.

### Tilemap Purposes
- `rhysh-tilemap`  Root Layer -     Contains information needed to create the form of a tile, walls, floor etc. 
- `rhysh-extra`    Extra Layer -    Commonly used icons used to indicate common things like doors and stairs.
- `kenny-bw-small` Extended Layer - Icons from Kenny's one-bit pack [ https://kenney.nl/assets/bit-pack ] used to map a wide variety of extensions onto a tile.

### FeatureTypes
- `PrefabChunk` A complete chunk. Prefab chunks can still define different regions within for random generation.

### Extensions
While some extensions (such as ReturnToTown and OriginPoint) are unique most will be able to be reused in other maps
- `Battle:(table)` Start a battle using the monsters found in the encounter table.
- `EventTrigger:(code)` Adds an event to the queue. If the event's requirements are met it's shown immeadietly. 
- `Treasure:(table)` Adds a simple event generating some treasure for the party. Should only happen once. The table parameter is used to define which treasure table is used to randomly select treasure from.

