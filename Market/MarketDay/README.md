Market Day: Sell Your Items
===

Every Saturday, Stardew Valley hosts a Farmers Market in the town square. This is your chance to showcase the best produce, crafts, and artisan goods from your farm. You'll be joined by a random assortment of your fellow villagers, all selling the things they make or grow. 

Did you ever wish the Stardew Valley Fair was more interactive? This mod lets you sell your quality produce and buy the best items from other farmers.

You get a shop! Arrange your goods how you like, make a sign, choose a theme color, and put on a tempting display. The better your goods and arrangement are, the more sales you'll get.

Expect lots of customers. Villagers will walk past, stop and browse, and maybe let you know how they feel.

If you see something nice at another villager's shop, you can walk over and buy it. Prices will vary from bargain to ridiculous. It is a market after all.

The shops are different each week. If you see something you like, don't miss out! It might not be there next week.

How to use 
---

* The empty shop is yours. The other shops should be in use by other characters.
* Click on the lower edge of the grange stand to open up the shop.
* Add up to 9 items in whatever arrangement you like.
* Put extra items into the chest beside your shop.
* While you're in the chest, choose a color for your shop.
* Choose an attractive item to put on your shop's sign. It pays to advertise.
* Profit!

How to shop
---

* Click on the lower edge of the character's grange stand to open up the shop.
* You can buy any of the items on display, if you can afford it...

How to maximize your profit
---

* Grange display rules apply. If the items in your display would get a good score at the Fair, you'll make more money on Market Day.
* Showcase your best produce on your shop's sign.
* Villagers pay more for the things they love. They won't buy stuff they hate, obvs.
* Talk to your customers, it's good for business.
* Your shop is still open when you're not there, but you'll get a better price when you're nearby.
* Stock the shop yourself if you want the absolute best layout... or put excess items in the storage chest for automatic restocking.

Villagers you might see
---

* Vanilla: Marnie, Evelyn, Gus, Leah, Emily, Clint, Jodi, Harvey, Linus, Willy
* SVE: Sophia, Andy, Susan

Configuration
---

* Use Generic Mod Config Menu. You can change various things.
* In [CP] Market Day, you can change MarketLayout to choose different layouts. The available options are: 1 Shop, 3 Shops, 5 Shops, 7 Shops, 9 Shops
* If you change the day of the markets, make sure you change it in Market day AND [CP] Market Day. 

Requirements 
---
* Content Patcher
* Expanded Preconditions Utility
* Generic Mod Config Menu (optional)
* JSON Assets (optional)

Future shop ideas
---
* Haley sells photography
* Emily sells random clothes
* Robin sells furniture
* More art for Leah
* Sandy

Adding your own shops
---

Copy the format from the included shops.json to make new GrangeShops. [ItemShops from STF](https://github.com/ChroniclerCherry/stardew-valley-mods/tree/master/ShopTileFramework#create-a-content-pack) should be very compatible (because this mod reuses STF's loader code). Two more fields are available for GrangeShops:

* SignObjectIndex: the [ID](https://stardewids.com/) of the item that should go on the sign. e.g. `428`
* ShopColor: the RGBA color the storage chest should be set to. e.g. `"143, 0, 255, 255"`

Future
---

* write tutorial here
* allow tool sales
* test with animals and hats and such
* shut the market down properly at closing time, empty the granges
* get shop portraits made
* in-game tutorial
* do weighted random when stocking the grange so that 1-stack items don't dominate
* send a mail on the day before the first fair?
* check alternate currency support for STF packs
* tint the sign
* boost price if item is in season
* custom sell prices for player store with prob of purchase

License
---

GPLv3

Credits
---

* by ceruleandeep
* Uses code from ChroniclerCherry's Shop Tile Framework
* Uses code from aedenthorn's Persistent Grange Stand


Developer Notes
===

Market Data Model
---
A list of co-ordinates of the grange stands (top left corner),
and a map of shop name -> NPC who runs the shop.

```json
{
  "ShopLocations": [
    "28, 58",
    "28, 63",
    "33, 63",
    "33, 58",
    "33, 68",
    "28, 68",
    "23, 63"
  ],
  "ShopOwners": {
    "Alex": "Alex",
    "SVColaShop": "Vincent"
  }
}
```

Grange shop life cycle
---

Day started:
* reset the sales and visitor counters
* reset the lists of sales and recent buyers
* find or create the chest and sign objects
* put some stock in the chest, if an NPC store
* restock the shop from the chest
* move the furniture into position

When world is rendered:
* draw the shop contents

When button pressed:
* if a player shop, open the grange menu
* if a NPC shop, open the shop menu

Every hour:
* move some items from the chest to the shop

Every update:
* if an NPC is in front of the shop, stop to look

Every 1s update:
* if an NPC is looking, maybe sell something to them
* if the NPC store owner is nearby, stop to tend the shop

Day ended:
* if a player shop, move the shop stock into the chest and hide the furniture
* if a NPC shop, destroy the furniture

NPC scheduling
---
```
Game1::_newDayAfterFade()
// newDaySync.barrier("removeItemsFromWorld");
-> NPC::dayUpdate()
// newDaySync.barrier("buildingUpgrades");

-> NPC::resetForNewDay()
-> NPC::getSchedule()
-> NPC::parseMasterSchedule()
-> NPC::pathfindToNextScheduleLocation()
-> PathFindController::findPathForNPCSchedules()
```

etc
---

patch reload ceruleandeep.MarketDay.CP
patch summary ceruleandeep.MarketDay.CP
patch summary ceruleandeep.MarketDay

  "37": "Wood Sign/5/-300/Crafting -9/Use an item on this to change what's displayed. The item won't be consumed./true/true/0/Wood Sign",
  "130": "Chest/0/-300/Crafting -9/A place to store your items./true/true/0/Chest",
  "232": "Stone Chest/0/-300/Crafting -9/A place to store your items./true/true/0/Stone Chest",


Object
    Object::performToolAction
        public virtual bool performToolAction(Tool t, GameLocation location)
        
        this.performRemoveAction((Vector2) (NetFieldBase<Vector2, NetVector2>) this.tileLocation, location);
        if (location.objects.ContainsKey((Vector2) (NetFieldBase<Vector2, NetVector2>) this.tileLocation))
          location.objects.Remove((Vector2) (NetFieldBase<Vector2, NetVector2>) this.tileLocation);
              
    Object::performRemoveAction
    
    Object.fragility
    
    Object.isTemporarilyInvisible
    
    public virtual bool placementAction(GameLocation location, int x, int y, Farmer who = null)
        makes a fresh one of whatever item the player is trying to place
        if returns true, players stack of the item is reduced by 1
        sets the owner
        for a Chest:
            if location.objects.ContainsKey(placementTile) || location is MineShaft || location is VolcanoDungeon:
                unsuitable location, return false
            make a new Chest(true, placementTile, this.parentSheetIndex);
            add to location.objects at x, y
        for a Sign:
            location.objects.Add(placementTile, (Object) new Sign(placementTile, this.ParentSheetIndex));
        


Chest
    type: "Crafting" or "interactive"
    chestType: "Monster"
    playerChest: 
    TileLocation:
    
    interactive chest ctor:
    public Chest()
    public Chest(Vector2 location)
    public Chest(string type, Vector2 location, MineShaft mine)
    public Chest(int coins, List<Item> items, Vector2 location, bool giftbox = false, int giftboxIndex = 0)
    
    crafting chest ctor:
    public Chest(bool playerChest, Vector2 tileLocation, int parentSheetIndex = 130)
    public Chest(bool playerChest, int parentSheedIndex = 130)
     
    public override bool performToolAction(Tool t, GameLocation location)
        if TileLocation is 0,0 try and find the tile from location.Objects or use the tool's target tile
        if empty: 
            performRemoveAction
            location.Objects.Remove(tileLocation)
            something about debris
       
    MoveToSafePosition
    public bool MoveToSafePosition(
          GameLocation location,
          Vector2 tile_position,
          int depth = 0,
          Vector2? prioritize_direction = null)
       
    placementAction
    public override bool placementAction(GameLocation location, int x, int y, Farmer who = null)
        basically calls base
    