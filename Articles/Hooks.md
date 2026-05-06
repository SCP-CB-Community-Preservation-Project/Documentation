# Hooks

Hooks allow your scripts to define functions that are called at various points throughout the game.

Some hooks allow returning a `bool` in order to completely override the behavior of the base game, as well as any mods that are loaded before your mod.

> [!warning]
> Hooks that allow returning a `bool` *MUST* return one, in order to prevent undefined behavior from occurring.

## void Hook_Initialize()

Called when your mod is first loaded.

This is where custom image, font, shader and audio resources, as well as custom options can be loaded.

> [!caution]
> All entities, brushes and textures are freed when quitting to the main menu. Therefore, these should *NOT* be loaded here.

## void Hook_SaveOptions()

Called when the game's options are being serialized to the `options.ini` file, allowing your mod to serialize its own options with it as well.

> [!tip]
> When saving your custom options, prefix them with an identifier that is (hopefully) unique to your mod, in order to prevent potential conflicts with other mods.

## void Hook_Update()

Called once per frame after the game has finished its processing and rendering, but before the rendered image is displayed to the screen.

## bool Hook_DrawHUD()

Called before the HUD is drawn.

## bool Hook_MovePlayer()

Called before the player character is moved according to the current game states and the player's inputs.

## bool Hook_MouseLook()

Called before the player's mouse input is translated to the player camera's pitch and yaw.

## bool Hook_KillPlayer()

Called before the player character would be killed. The player is not killed when overriden.

## bool Hook_InitializeEvents()

Called before the events for a newly created save are initialized. Allows initializing custom events via @ref CB::Event::Create.

## bool Hook_UpdateEvent(@ref CB::Event \@)

Called for every event every frame. Used to add behavior to custom events or modify the behavior of existing events.

## bool Hook_FillRoom(@ref CB::Room \@)

Called when a room is created, before it is filled with doors, items and other entities. Used to add entities to custom rooms or modify the entities found in existing rooms.

## void Hook_PostFillRoom(@ref CB::Room \@)

Called when a room is created, after it has been filled with doors, items and other entities. Used to manipulate the entities the room has been filled with.

## bool Hook_LoadRoomTemplateEntity(@ref CB::RoomTemplate \@, int rMeshVersion, @ref B3D::Stream \@, string entityName)

Called before a map entity is loaded from an .rmesh file. Can be used to implement custom map entity types or override how existing entity types are loaded.

> [!tip]
> When implementing a custom map entity type, include a version byte in order to support backwards compatibility.

## bool Hook_UpdateItem(@ref CB::Item \@)

Called for every item every frame. Can be used for custom item logic. Handles gravity, reachability calculations for being picked up by the player and item-push behavior unless overriden.

## void Hook_RemoveItem(@ref CB::Item \@)

Called when an item is removed from the world and freed.

## bool Hook_PickItem(@ref CB::Item \@)

Called when the player tries to pick up an item with sufficient inventory space remaining. The item is not added to the player's inventory when this function is overriden.

## bool Hook_DropItem(@ref CB::Item \@)

Called after the player dropped an item from their inventory.

## void Hook_SelectItem(@ref CB::Item \@)

Called when the player uses an item from their inventory.

## void Hook_CombineItems(@ref CB::Item \@ draggedItem, @ref CB::Item \@ draggedOntoItem)

Called when the player tries to combine two items.

## void Hook_CreateNPC(@ref CB::NPC \@)

Called before an NPC is initialized according to its @ref CB::NPC::Type.

## void Hook_PostCreateNPC(@ref CB::NPC \@)

Called after an NPC is initialized.

## bool Hook_UpdateNPC(@ref CB::NPC \@)

Called for every NPC every frame. Used to add behavior to custom NPCs or modify the behavior of existing NPCs.
