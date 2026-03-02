# Mechanics

## Layers
Being a 2.5D game (2D with a discretized Z axis), the graphics will be layered. There will be two types of layers: planes (major layers), which represent different discretized levels of the Z axis, and layers (minor layers), which compose a single plane.

## Map chunks
The world is broken into map chunks, 32x32 groups of tiles (among other information, such as what items, npcs, and atmosphere exist on that layer), segregated into planes. Each plane broken into layers. Each map chunk has an id which is the X,Y coordinates, representing the coordinates of the map chunk's top left corner on the world map among all other map chunks.

## Camera
The player will always be centered in the view while not in battle. When transitioning between planes, the destination plane scales to match the scale of the player sprite. Other planes scale accordingly, with lower-visible planes scaled to be smaller than the player's plane, and higher planes to be larger.

## Asset loading/unloading & rendering
The camera position + screen size determines which assets are loaded. Given the player's position its easy to tell which map chunks need to be loaded. The planes will not have negative indexes, and there will be a max index.

From very high vantage points, more map chunks might need to be loaded, given that lower planes will be scaled down significantly and more of them can fit in view.

Planes above the players current with visible layers overlapping with the player's position (or positions within a certain radius of the player), will be transparent. This way effects like canopies, trees, tall buildings and more can be seen but not interfere with the seeing the player.

## Movement basics
The movement is discretized along lattice paths. Movement is also enabled between layers in specific situations, such as ladders, doors, ramps, and teleporters.

Moving will generally be between lattice points, but when moving along the Z axis, movement will also entail scaling assets.

## Tiling & Tile types
A tile is a collection of all the layer information for a given X,Y coordinate on the map, for a given plane.

Tiles will have coordinates corresponding to their position within their map chunk. These coordinates can be converted to world coordinates by adding their map chunk's world map chunk coordinates multiplied by 32. E.x. tile 1,1 on map chunk 3,5 has world coordinates of 97,161.

Like map chunks, tiles coordinates reflect the position of their top left corner.

Unlike map chunks and tiles, the player, npcs, items, doors, ladders, and other interactive objects exist on an offset lattice. This lattice is offset a half tile vertically and horizontally. This means the player, and other objects the player will need to be aligned with, don't straddle multiple tiles. If the movement and tiling lattices were one, the player would, at any given stationary position, straddle 4 tiles. The purpose of this is to simplify the art.

## Gadgets
Special items that allow the player to interact with the map differently.

### Raft
Allows the player to move in water (some exceptions apply).

### Diving Bell
Allows the player to dive beneath non-shallow water to explore underwater areas. (underwater areas have 'time limits' in the form of a max step count the player can take in the diving bell before they must return to the surface)

### Scooter
Allows the player to move faster outside of buildings.

### Hearing Aids
Allows the player to 'overhear' conversations from a distance.

### Champion's Belt
Allows the player to move heavy objects (rocks and logs).

### Scythe
Allows the player to cut tall grasses.

### Metal detector
Notifies the player when they are within five moves of a hidden item.

### Cliff Pole
Allows the player to vault drop down from a single plane.

## Interactive Objects
There will be specialized state for storing the positions of interactive objects. This will store things such as positions and directions of NPCs, positions of rocks/logs, etc.

### Boulders
A round stone that can be pushed one lattice length at a time away from the direction of the player. Boulders are stateless. So that boulders respawn in correct positions and properly track wether they've been moved to final resting places (the positions that they need to be in to trigger certain effects or solve certain puzzles).
When pushed into shallow water, they create stepping stones.

### Logs
Logs are just like boulders but can only be moved in the direction perpendicular to the direction of their length. Logs, when dropped into flowing water, float downstream until they hit a bend/obstacle.

### Tall Grass
Non-traversable territory that can be temporarily cut to short grass with the scythe.

### NPCs
NPCs have directions, ids, and conversation ids each paired with a conditional. They can move and interact with tile metadata to know if there are any boundaries they cannot cross.

## Dialog
Dialog/conversations are trees. each node has: options, lines, battle ids, rewards, and/or quest updates.

Because the protagonist is silent all the decisions will be of on-verbal activities, like simple pantamimable phrases like 'yes' or 'no'. Or, actions which require no words from the players: "wait". 

## Quests
a dict of objects with phases flags.

e.x.:
{
  mainQuest: {
    phases: {
      meet_ikrus: false,
      ...
    }
  },
  ...
}
