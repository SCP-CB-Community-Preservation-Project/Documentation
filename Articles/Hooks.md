# Hooks

Hooks allow your scripts to define functions that are called at various points throughout the game.

Some hooks allow returning a `bool` in order to completely override the behavior of the base game, as well as any mods that are loaded before your mod.

> [!warning]
> Hooks that allow returning a `bool` *MUST* return one, in order to prevent undefined behavior from occurring.

## void Hook_InitializeLauncher()

Called when the launcher is initialized.
Only called when the launcher is enabled.

Custom resources used in the launcher should be loaded here.

> [!caution]
> Images that are loaded here will be freed when proceeding from the launcher.

## void Hook_Initialize()

Called when the game is initialized.

This is where custom image, font, shader and audio resources, as well as custom options can be loaded.

> [!caution]
> All entities, brushes and textures are freed when quitting to the main menu. Therefore, these should *NOT* be loaded here.

## void Hook_Shutdown()

Called when your mod is unloaded.

Resources loaded in `Hook_Initialize` should be freed here.

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

## bool Hook_ExecuteConsoleCommand(@ref string cmd)

Called when a console command is being executed by the player. Used to implement custom console commands or modify the behavior of existing ones.

## bool Hook_LoadEntities()

Called when the game transitions from the main menu to loading a save. Can be both an existing or new save. Can be used to load static resources like textures, entities and brushes that are needed in-game.

## bool Hook_InitializeEvents()

Called before the events for a newly created save are initialized. Allows initializing custom events via @ref CB::Event::Create.

## bool Hook_UpdateEvent(@ref CB::Event)

Called for every event every frame. Used to add behavior to custom events or modify the behavior of existing events.

## bool Hook_FillRoom(@ref CB::Room)

Called when a room is created, before it is filled with doors, items and other entities. Used to add entities to custom rooms or modify the entities found in existing rooms.

> [!CAUTION]
> The room entity may be rotated and moved after this hook has been called.
> All entities created in this hook SHOULD be parented to the @ref CB::Room::Object.

Example:
```cs
bool Hook_FillRoom(CB::Room r) {
    if (r.Template.Name == "myroom") {
        Item it = Item("key6", r.X, r.Y + 100.0 / 256.0, r.Z);
        it.Collider.Rotate(0.0, r.Angle + 45.0, 0.0);
        it.Collider.SetParent(r.Object);

        return false;
    }
    
    return false;
}
```

## void Hook_PostFillRoom(@ref CB::Room)

Called when a room is created, after it has been filled with doors, items and other entities. Used to manipulate the entities the room has been filled with.

## bool Hook_LoadRoomTemplateEntity(@ref CB::RoomTemplate, int rMeshVersion, @ref B3D::Stream, @ref string entityName)

Called before a map entity is loaded from an .rmesh file. Can be used to implement custom map entity types or override how existing entity types are loaded.

> [!TIP]
> When implementing a custom map entity type, include a version byte in order to support backwards compatibility.

## void Hook_PostLoad()

Called when loading is finished and control is about to be handed to the player. Called after the player has pressed any key to continue.

## bool Hook_CreateItem(@ref CB::Item)

Called when an item is created. Handles initialization of inventories for inventory items like wallet and clipboard unless overriden.

## bool Hook_UpdateItem(@ref CB::Item)

Called for every item every frame. Can be used for custom item logic. Handles gravity, reachability calculations for being picked up by the player and item-push behavior unless overriden.

## void Hook_RemoveItem(@ref CB::Item)

Called when an item is removed from the world and freed.

## bool Hook_PickItem(@ref CB::Item)

Called when the player tries to pick up an item with sufficient inventory space remaining. The item is not added to the player's inventory when this function is overriden.

## bool Hook_DropItem(@ref CB::Item)

Called after the player dropped an item from their inventory.

## void Hook_SelectItem(@ref CB::Item)

Called when the player uses an item from their inventory.

## void Hook_CombineItems(@ref CB::Item draggedItem, @ref CB::Item draggedOntoItem)

Called when the player tries to combine two items.

## void Hook_CreateNPC(@ref CB::NPC)

Called before an NPC is initialized according to its @ref CB::NPC::Type.

## void Hook_PostCreateNPC(@ref CB::NPC)

Called after an NPC is initialized.

## bool Hook_RemoveNPC(@ref CB::NPC)

Called before an NPC is about to be removed. Can be used to prevent an NPC from being removed. There is no danger of persisting NPCs when transitioning to the main menu, as they are deleted separately then.

## bool Hook_UpdateNPC(@ref CB::NPC)

Called for every NPC every frame. Used to add behavior to custom NPCs or modify the behavior of existing NPCs.

## CB::NPC::Type Hook_ConsoleNPCNameToType(@ref string name)

Called when an NPC's name is to be parsed from console input.
Returning `-1` will delegate responsibility to lower-priority mods and the base game.

## string Hook_ConsoleNPCTypeToName(CB::NPC::Type type, bool isPlural)

Called when an NPC's name is to be printed to the console.
Returning `""` will delegate responsibility to lower-priority mods and the base game.

## int Hook_ConsoleSpawnNPC(CB::NPC::Type type)

Called when an NPC is to be spawned from the console.
Returning `-1` will delegate responsibility to lower-priority mods and the base game.
Returning `0` will disallow the NPC from being spawned via the console.
Should only be overriden for custom NPCs when specific changes are necessary to make the NPC function correctly upon creation, in which case `1` must be returned.
By default, NPCs are created 0.2 units above the player collider's position.

## int Hook_ConsoleCheckCanChangeNPCSpeed(CB::NPC::Type type)

Called when an NPC's speed is about to be changed via the console.
Returning `-1` will delegate responsibility to lower-priority mods and the base game.
Should only be overriden for custom NPCs to indicate that it is unaffected by the @ref NPC::Speed value by returning `0`.
Returning `1` will indicate that the NPCs speed can be changed (the default for custom NPCs).
Can also be overriden to indicate that a script modification to a base game NPC will allow it to be affected by the @ref NPC::Speed value.

## int Hook_ConsoleCheckCanToggleNPC(CB::NPC::Type type)

Called when an NPC is about to be enabled/disabled.
Returning `-1` will delegate responsibility to lower-priority mods and the base game.
Must only be overriden when implementations for `Hook_EnableNPC` and `Hook_DisableNPC` are provided for an NPC type, which is supported in the base game, in which case `1` must be returned.
Returning `0` will indicate that the NPC cannot be enabled/disabled (the default for custom NPCs).

## bool Hook_EnableNPC(@ref CB::NPC)

Called when an NPC is to be enabled.

> [!NOTE]
> It is not guaranteed, that the NPC has previously been disabled.
> This hook may be called on already enabled NPCs.

> [!IMPORTANT]
> `Hook_ConsoleCheckCanToggleNPC` must be overriden to allow for this hook to be called.
> `Hook_DisableNPC` must also be overriden to actually do the disabling, which this hook undoes.

## bool Hook_DisableNPC(@ref CB::NPC)

Called when an NPC is to be disabled.

> [!NOTE]
> It is not guaranteed, that the NPC has previously been enabled.
> This hook may be called on already disabled NPCs.

> [!IMPORTANT]
> `Hook_ConsoleCheckCanToggleNPC` must be overriden to allow for this hook to be called.
> `Hook_EnableNPC` must also be overriden to undo the disabling.
