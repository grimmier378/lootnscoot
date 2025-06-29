# Changelog

All notable changes to this project will be documented in this file.

## [unreleased]

### 💼 Other

- Jun 29, 2025, 11:13 AM
- Jun 29, 2025, 11:16 AM
- Jun 29, 2025, 11:28 AM
## [release] - 2025-06-29

### 🚀 Features

- Feat: Help WINDOW, fix: restock fixes (#31)

remove duplicate actor messages (#30)

Added help window with lists of commands and settings. `/lns help` or there is a toggle on the main UI

`/lns bank` was a duplicate command

now to bank with targeted banker use `/lns bankstuff`

`/lns bank` is still reserved for setting items on cursor or specified items rule to Bank
- Feat: Import Old Database Rules (#36)

* Added command `/lns importold` incase you already had a `LootRules_Server_name.db` file in your MQ/Config folder and wanted to import those.

* this will open a window for you to enter the db name (including .db at the end) and import everything with a button click.

* any items in the db we couldn't find or that had more than one item matching that name will have a negative ID in the DB for now.

* Items with negative ID's or mossing items will have a red button with an X on it instead of the green edit pencil =)

* Clicking the button will bring the normal modify items window but if we find more than one item with that ID we will list them below so you can click and view their data to decide and set the right one.

* If we find an item while looting that matches one of the `missing items` we will update that item rule with the proper ID. assuming this item has 0 other matches on the name. aka first time seeing one named that.
d0a66f

### 🐛 Bug Fixes

- Fix: logger crash (#21)

forgot to pass a console in loot_hist
- Fix: some fixes and cleanup (#22)

* fix: some fixes and cleanup

* removed a bunch of unused code
* moved looted config so it doesn't get corrupted.
* cleanup some reporting
- Fix: Forage and mycorpse check fixes (#23)

* mycorpse check adjustment

* fix:  mycorpse check

should now properly stop looting when you have a corpse of your own near by

* fix: foraging

tweaks to the foraging detection and processing
- Fix: bug fixes and new setting (#29)

fixes some value mismatch crashes and some typo's

added `LootCheckDelay` Setting this is how long in seconds we will wait before trying to check corpses again.

cleaned up the loot my corpse checks and when we do the looting.
- Fix: trying to fix occasional stuck open loot windows (#32)

found some extra spots to ensure we close the window when its not supposed to be open.
- Fix corpseName nil error
- Fix: restock var mismatch and timing (#34)
- Fix: buy items saving qty as strings (#35)

* fix: buy items saving qty as strings

* fix: crash trying to open new item table when empty

fixed a crash if you tried to open the new items window without new items from the mini button context.

also hide the context menu item if no new items

* fix: Manual Looting windows closing out issue

will abort lootMobs() routine if already looting
- Fix: decision checks (#38)

* decision checks should properly check classes
* decision checks will honor the `CanUse` tag better.
* prevent `finditemid` lookups from locking tables.
- Fix: ML stuck loops (#39)

* clear the items to loot and ML tables on zoning so we don't beeline to a corpse a cross the zone when coming back after wiping.

* fixed the stuck loop when a corpse or item is missing and we get stuck trying to loot and remove it over and over. had the index value wrong.

* Added combat checks when directing a character to loot. combatlooting setting will be checked as well. otherwise we will wait till after combat to try and loot the item.

### 💼 Other

- Adv looting rewrite (#20)

# Loot N Scoot (Rewrite) ~ Now with more adv looting features.~

Because of the rewrite to fix some issues and add more advanced looting mechanics. There is a link to the old version on the Github Releases page.
https://github.com/aquietone/lootnscoot/releases/tag/Classic_Version

All configs, tables, etc are kept separate between the new version and the old. so you can swap back and forth if you so choose. 

### New Checks
- Check invis and don't loot if invis.

### Decision and Rules Logic
- Decision making happens for each item looted. and overrides some rules based on our settings. 
 - Items Marked as Sell will be checked to meet our settings thresholds before looting.
 - the same goes for items marked as Tribute
- New items that have a sell value will default to sell as the new rule, and similarly for tribute
- You will still see the new item popup and are more than welcome to change the rule. 
- NoDrop items we first see, default to `Ask` 
 - if LootNoDrop is enabled we will check against the `CanWear`  setting and it it is something we can wear we will keep otherwise ignore.
 - if `CanWear` is disabled we will just `Ask`

### New Mode
- Current modes of operation are 'once', 'standalone', and now 'directed'
  - Once is what happens when you call `/lua run lootnscoot` without any arguments or with arguments to sell,restock,tribute,bank etc.
  - Standalone is when the script is constantly running and doing its thing in the background `/lua run lootnscoot standalone`
  - Directed is where you call the script from another script `/lua run lootnscoot directed`
    - This mode will only loot when told over Actors or you directly issue the `/lns loot` command
    - If  you include the lootnscoot folder in your scripts folder you will want to make the path `yours_script_folder/lib/lootnscoot` and you would load from inside your script with `/lua run yourscript/lib/lootnscoot directed scriptname`
    - Check out the RGMercs Lua for an example of how to implement this.

### Items Table DB Changes
- The items DB will auto-add/update items as you and your party loot them.
- The items table has been changed to include more details about the items.
- Items are now keyed by the item ID:
  - Using item ID, we can now handle items where there are more than one with the same name but different stats/flags.
- The DB will also store link information so you can access loot information from the table with a right-click.
- a Default Items.db is avail [Here](https://github.com/grimmier378/emu_itemdb/releases) this has all of the items dumped from EQEMU server defaults. Custom servers will be missing any customized items. 
 - if your server has modified existing items you will get updated information in the db as you loot the item or import / add it. the links should still point to the correct itemID and show properly from the server either way.

### ADV Loot Rules DB
- A new rules DB accounts for using item IDs. Unfortunately, this invalidates the old rules table since we never had the ID data.
- NEW Rules Section (Personal Rules)
  - This is a per character set of rules for items.
  - These rules will override all other rule sets. 
  - Order of Authority `Personal > Global > Normal`

### Config and DB Locations
- **Config Files:**
  - Now reside in `mqfolder\config\LootNScoot\ServerName\` to keep things organized.
  - Changed the config file format to `.lua` for easier loading and saving.
- **DB Files:**
  - Reside in `mqfolder\resources\LootNScoot\ServerName\`.
  - Keeps data organized, especially if you play on multiple emulated servers, preventing overwrites.

### New Loot Pop-up / Tab
- Displays information so you can decide on rules for items looted without existing rules in the DB:
  - Item name, sell price, stack size, nodrop, lore, augment, tradeskill.
- On the first loot, the decision is based on your settings, with options to change them.
- **Optional Features:**
  - A pop-up window showing undecided items for the session.
  - A new "Items" tab in the loot window.
- **Additional Functionality:**
  - A button with an eye icon opens the inspect window for item stats.

### Item Lookup Tab
- Look up any item from your DB:
  - Shows items you have rules for or previously looked up.
  - With a fully exported DB from EQEmulator, over 117k items are available (before custom items).
- **Interactions:**
  - Right-clicking an item opens the inspect window.
  - Left-clicking an item opens the ModifyItem Rule Window to add a rule for the item.
- **Customizations:**
  - Right-clicking table headers allows toggling fields and rearranging their order.
  - Drag-and-drop items to add them to the table if missing.

### Bulk Set Item Rules
 - You can set the entire page worth of items to the same rule with the bulk set option. 
 - This will do only the items that are on the current page of your search results. 
 - You can adjust the number of items displayed in Increments of 10 
 - Added option to bulk delete rules from the current table
 - Added checkboxes to the items tables, you can select items from multiple pages. 

### Rules Tables
- **Searchable:** Filter by item name or rule type (e.g., Quest, Bank).
- **Right-click Interactions:** Opens the inspect window in-game.
- **Stored Links:** Includes links in case the items DB is deleted or rules DB is copied from another server.
- **Edit Item Button:**
  - **Green Button with Pencil:** Indicates the item exists in the main items DB.
  - **Red Button with X:** Indicates the item is missing from the Items DB but has a rule.
  - Clicking the button opens the ModifyItem Rule Window.

### Import Items
- Import items from your character's inventory/bank with `/rgl importinv`:
  - Bank data may not be fresh but refreshes when zoning or at the bank.
  - Useful for starting with an empty DB or migrating to a new server.

### Loot Settings
- **Loot Corpse Once:** Tracks looted corpses to avoid re-looting.
  - Unlocks the corpse if a new item rule is changed (e.g., from "Ignore" to another flag).
- **Can Use Only Flag:**
  - Loots only items you can use/wear flagged as "KEEP," either via rules or evaluations.
  - Ignores "Sell," "Bank," "Quest," etc., flags unless explicitly allowed.
  - Helpful with `lootnodrop` and `lootnodropnew` enabled.
- **KeepSpells:** Keeps all spell drops regardless of rules or sell values.
- Actors Settings:
 - You can edit any characters settings from the window, clicking save will send them the new settings and they will save them.
 - You can clone a characters settings from one char to another. making setting up new groups easier.
- Added `IgnoreBagSlot` setting. setting this will ignore anything in that slot\bag when it comes to selling\tributeing\banking\cleanup.
- AlwaysGlobal: (default off)
 - This will also make a global rule for new items when looting and when acknowledging the new item rules. 

### COMMANDS
- `/lns` or `/lootutils`
- Arguments
  - `sellstuff`         sell to targeted merchant
  - `restock`           restock items from targeted merchant (must be in buy table)
  - `tributestuff`      tribute items to the targeted merchant
  - `bank`              bank items to targeted banker
  - `show`              shows loot gui
  - `report`            shows report table 
  - `shownew`           shows the new item table
  - `set settingname value` example /lns set doquest on will till looting quest items on  or /lns set minsellprice 1 will set minsellprice to 1
  - `cleanup`           clean up bags or items marked 'Destroy'
  - `importinv`         imports your inventory and bank (if avail) items into the main items database
  - `pause`             pauses looting
  - `resume`            unpauses looting
  - `debug`             enable debug spam in loot console.

### NO DROP
- if `lootnodrop` is enabled you will loot items marked nodrop that you have rules for
- if `lootnodropnew` is enabled you will also loot new items that are nodrop
- regardless of these settings a new item with no rules that is nodrop will have the rule set to CanUse to prevent the first person deciding its ignore.

### UI
- New Graphical toggle button
  - Shows as a Plat Coin or a Gold Coin when there are new rules to confirm
  - Left Click will toggle the main LootNScoot window, you can also `/lns show`
  - Right Click will open the New Items window (if there are any new items to confirm) or you can also `/lns shownew`
- Main LootNScoot Window
  - This is where you will find buttons to open the console, loot report table. 
  - You will also find the settings and Item rules and lookup tables here. 
- Historical Data Window
 - You can view recoded Historical Data from in game and search using most of the fields.

## Bank Tradeskill Changes
the bank tradeskills flag will no longer auto flag items to bank

Instead it will be checked when issuing the bank command.

When banking:
check rules and if its 'Bank' toss it into the bank
if the rule is not Global or Personal then check if the item is a TS item, and if we have Bank TS enabled.
if both are true then bank it.

this way we don't have rules flip flopping from this setting. and it still serves a purpose for items without overrides in global or personal rules tables.

MEDIA

![image](https://github.com/user-attachments/assets/db9acb46-82ce-4c60-b76c-ee461785f619)

![Screenshot 2025-01-24 012020](https://github.com/user-attachments/assets/d313e977-196f-4952-bb37-3823f58404e3)

![Screenshot 2025-01-24 012123](https://github.com/user-attachments/assets/a37f7d26-ab3a-48fb-af66-db23b0852cc6)

![Screenshot 2025-01-24 012151](https://github.com/user-attachments/assets/782ba0a0-b58a-4bd1-9089-a610765c79ba)

![Screenshot 2025-01-24 012212](https://github.com/user-attachments/assets/7b58534f-e234-4f6e-897f-892045dbeafa)

![Screenshot 2025-01-24 021107](https://github.com/user-attachments/assets/7521f1ed-ffa5-4c8a-990d-a5cc4b7606a0)

[VIDEO PREVIEW](https://youtu.be/evhK7QYadxg)

[VIDEO PREVIEW4](https://www.youtube.com/watch?v=k3nETo_JStE)

[VIDEO PREVIEW5](https://youtu.be/_ebgzWZs33w)
- Directed adjustments (#25)

for handling calling LNS from its install folder and not being bundled
You can call it in directed mode either way by bundling it into your lib/lootnscoot folder or just pass lootnscoot as the 3rd parameter to call it from its own standalone install folder (this should make it easier than having to maintain LNS inside other scripts)
- New settings, fixes, tooltips and more (#27)

* Added New setting `IgnoreMyNearCorpses` for those servers where you keep your gear on death.
* Added `SafeZones` see Settings panel We will NOT try to loot if in a `SafeZone`.
* Added Tooltips to the settings
* Added a button to the GUI to toggle Debug spam
* Another pass on directed mode.
* Another pass on loot rule checks (hopefully this fixes the occasional looting of `NoDrop` by the wrong class, looking good so far in testing)
* Performance pass on Actors messaging to cut down on how often messages are sent trimming duplicate sending.

Directed Mode Adjustments

* Added 3rd argument when launching for the scripts location (default `YourScriptName/lib/lootnscoot`) you can also just pass `lootnscoot` to have your script call LNS from its native install if it exists.
  * `/lua run ScriptPath directed DirectorName ScriptPath` where:
    * `ScriptPath` is `lootnscoot` for the standalone as arguments 1 and 3 or your bundled path.  If bundled and not specified in the 3rd argument then it defaults to  `DirectorName/lib/lootnscoot`
    * `DirectorName` is the name of your script you are calling this from (name of folder in the lua dir)
   * If loading the default lootnscoot you should always add the 3rd field `lootnscoot`.
  * example from RGMercs Lua: In this example we also allow the user to choose which to load if they have LNS installed and the bundled version.
 ```
local Files                             = require('mq.Utils') -- has file exists function
local hasLootnScoot      = Files.File.Exists(string.format("%s/lootnscoot/init.lua", mq.luaDir)) -- check if they have lootnscoot installed
local bundleScriptPath   = string.format("rgmercs/lib/lootnscoot") -- set the bundled path
local LootScript         = "lootnscoot" -- default lootnscoot script name ie `/lua/lootnscoot` foldername
local scriptName = (Config:GetSetting('UseBundled') and hasLootnScoot) and bundleScriptPath or LootScript
mq.cmdf("/lua run %s directed rgmercs %s", scriptName, scriptName) -- call the script, by passing the 3rd argument we can use this in Actors mailbox addressing to refine the correct mailbox.
-- If you leave the 3rd one blank we will default to `DirectorName/lib/lootnscoot` if loading the default lootnscoot you should always add the 3rd field.
```
  * This should make it easier to maintain LNS in a script that is bundling it, by allowing  you to not have to bundle it
  * Saves on maintaining a copy every time LNS is updated.
* Added some more settings to be passed over actors to the `DirectingScript` so you can toggle them from both GUI's if you wanted.
- New setting and some fixes \ tweaks (#28)

* New Setting `MaxCorpsesPerCycle` sets the number of corpses we will try to loot each cycle,

* fixed some history reporting outputs, they would record correctly but display wrong on the session.

* Made the debug button a toggle for easier visibility
- Remove duplicate actor messages (#30)
- Master Loot Option (#37)

* You can enable `MasterLooting` in the settings or through `/lns set` command
* Changing the setting on one char will tell the others to match
* When MasterLooting is on we will report back any items not marked as ignore or destroy.
* the items will appear in a new window showing some basic information about the item,
* clicking on the item name will open its link in game
* the members column will list people from actors and how many of the item they have
* clicking the `loot` button next to a name will tell that person to loot the correct mob and one of those items
* users will nav to the corpse and attempt to loot one of the item specified.
* You can also dismiss an item form the list (may reappear on next corpse if another drops, you can set it to ignore to prevent reappearances)
* There is also a `Self Loot` button to just loot the item yourself.
- Include npc in xtarhater nearby mobs scan for buggy XT

### 🎨 Styling

- Style: clone settings gets its own window (#33)

cloning settings should now be more intuitive

clicking on the clone settings button will open a new window.

Select the source and destination users from the list on the left. Right click to bring up the selection context menu.

Once both are selected you can compare their settings side by side . Clicking the "Clone Now" button will send the new settings over to the destination user.
This will also unset the destination user so you can clone the same settings to someone else.

* Help window tables can now have their columns resized
## [Classic_Version] - 2024-12-02

### 🚀 Features

- Feat: auto restock option

if enabled will try to restock items from the current vendor after selling.
- Feat: Settings and loot table GUI

/lootutils settings

will toggle the settings and loot tables gui. so you can modify the settings in game.
- Feat: Settings and loot table GUI

/lootutils settings

will toggle the settings and loot tables gui. so you can modify the settings in game.

### 🐛 Bug Fixes

- Fixes

applied the commented changes.
- Fixes

* Added in the and mq.TLO.Cursor.ID() == corpseItemID check for safe measure.
* removed the redundant reporting on sales
- Fix: close from menu

* can close from menu now as well as command.
- Fix: close button

* Fixed the close button on title bar. it works now
- Fix: report performance

fix for reporting linked items.
now we store the link when looting, instead of passing each item through when reporting.

This removes the stutter when processing multiple items.
- Fix: typo , tribute

fixed a typo in the NoSlot event, preventing it from triggering.

#*#There are no open slots for the held item in your inventory#*#"

fixed a missed conversion before compare causing tributes to take exceptionally long.
- Fix breaking early
- Fix: overwriting action

added a check if the action is already set to 'Destroy' don't change it to 'Looted'

this way i can parse the destroyed actions and still show the items marked as destroyed in the report.
- Fix: removed theme stuff

I forgot to remove the theme stuff when i copied in the new version of looted.

this fixes that.
- Fix: primary key on db to be item name
- Fix: Forgot Normal_Rules had Global 2x

### 💼 Other

- Initial commit
- Update write.lua link
- Remove extra %s from loot reporting
- Bank all TS items util
- Remove extra function
- Clean up double loop for nodrop/lore, other cleanup
- NoDrop

Enabled the commented out code for NoDrop Confirmation Dialog
added setting LootNoDrop to Ini settings.
Re-ordered the looting checks. to check lore first, if lore and  we don't already own one check no drop check if we want to loot no drop.
if just no drop check if we want to loot no drop.
- Lib

lib folder with Write lua.
- NoDrop

changes proposed by aquietone.
- NoDrop

changes proposed by aquietone.
- Merge pull request #1 from grimmier378/NoDrop

NoDrop
- Settings split

* split the settings out of the loot.ini file, moved it to LootNScoot_Server_Name_CharName.ini
* Added a flag that we save to settings WasLooting
This is used for when we have full bags. 
*Added local tmpDoLoot stores ini DoLoot value so we we have a flag to change. 
* if Bags are full we set WasLooting to true and tmpDoLoot to false. until we have more bag space.
*Saving this WasLooting setting will allow keeping track if issuing /lua run lootnscoot once comamnds.
* This is helpful if you want to integrate into a macro like Kissassist. I actually use this method with KissCharm.
* Added Nil check for lore looting, since we are looking at strings 'nil' is a valid result and it skips looting.
* Added some reporting for lore items.
- Settings split

* split the settings out of the loot.ini file, moved it to LootNScoot_Server_Name_CharName.ini
* Added a flag that we save to settings WasLooting
This is used for when we have full bags. 
*Added local tmpDoLoot stores ini DoLoot value so we we have a flag to change. 
* if Bags are full we set WasLooting to true and tmpDoLoot to false. until we have more bag space.
*Saving this WasLooting setting will allow keeping track if issuing /lua run lootnscoot once comamnds.
* This is helpful if you want to integrate into a macro like Kissassist. I actually use this method with KissCharm.
* Added Nil check for lore looting, since we are looking at strings 'nil' is a valid result and it skips looting.
* Added some reporting for lore items.
- Versioning

* added version tracking on config.
This way we add settings automatically if something is new.
- Versioning

changed ordering a little.
- Saving waslooting setting when changed.

forgot to save the WasLooting setting
- Switched output to links

made the spam links for the nodrop items.
- Merge pull request #2 from grimmier378/settings_split

Settings, Lore, and tracking for full bags
- Update changelog
- *Quest Items*

* Added setting LootQuest default false
     * If set to true, and an item is marked as Quest in the loot.ini we check for a quantity
     * If the entry is listed like itemname=Quest|5 then the quantity will be 5 that we loot. if no quantity is set and its just Quest, we will default to the QuestKeep amount.
     * Will report the count if reportloot is on.
     * Stops looting that items and switches to Ignore once you reach your qty.
     * Checks both bank and inventory to get a total count.

* Cleaned up some of the bags full code, no need for tamp variables when we can just store BagsFull = true or false. Renamed WasLooting to BagsFull logically makes more sense.
- Better logic

no need to save if bags are full to file, just check it more often and store a variable.
Only report the first time it changes to true while actually looting.
- Better logic

no need to save if bags are full to file, just check it more often and store a variable.
Only report the first time it changes to true while actually looting.
- Better logic

no need to save if bags are full to file, just check it more often and store a variable.
Only report the first time it changes to true while actually looting.
- Switched from ignore to destroy

since if destroy is turned off we default to ignore.
- Cleaning

* Moved quest item reporting to better location.
* Moved destroy check to before confirmation window check incase fast destroy is off.
* Removed lore spam, since we already get the report at the end.
- Adjustments

LootItem takes an extra parameter for quest items to keep
getRule returns  the rule value and either a value for number of items to keep or 0 if not a quest item.
- Merge pull request #3 from grimmier378/quest_items

Quest Items
- Links

* items links for all reporting
- Always destroy

* Added Sales reporting. to see group sales totals from driver.
* All reporting should now use Item Links.
* Tweaked the settings layout to group settings better on initial creation.
* Moved destroy command farther down the decision making as it appears to not need confirmation windows even when set to always require them.  And Causes delays or skipped items it set before the confirmation checks.
* Added a setting to always destroy instead of Ignore.  **WILL NOT Destroy Lore Items Ignored because you already have one, WILL DESTROY Quest Items over Max, and Items Marked Ignore in config file** Requires DoDestroy = true, otherwise just Ignores Item.
**WARNING!! Recommend only turning this on for one character. Handy for cleaning up extra quest items after you hit the max while clearing the instance. 
* Quest Items we only set to destroy if we have max already.
- Merge pull request #4 from grimmier378/alwaysdestroy

Alwaysdestroy
- Tribute_stuff

* Added Settings:
* * TributeKeep keep items marked as tribute?
* * MinTributeValue min value to mark as tribute.
* * AlwaysEval re-evaluate items not marked as quest keep or destroy
* * BankTradeskills whether to auto set and bank tradeskill items.

* /lootutils
* * Now checks for item on cursor and will set value in the ini file.
* * /lootutils tribute will mark if on cursor or tribute items
* * /lootutils sell will mark if on cursor or sell

* AddNewSales wasn't working, added mq.doevents to loop to fix this.
* Added check if corpse is your own /lootall and bypass any checks.
- Minor cleanup

* minor cleanup and addressed the lua warnings to make them go away.
- Debugging

* /lootutils config will output your current config settings for that character.
* Added AddNewTributes setting functions same as AddNewSales
* Added mq.event for Tributing items.
* Added checks to not re-evaluate during selling and tributing only for when looting.
- Changes

* you can enter items either with it on cursor or through text.
* /lootutils quest name # for manually entering quest items with a vaule.  or /lootutils quest # if item is on cursor.
* changed /lootutils sell to sellstuff. should prevent confusing over sell for setting an item and sellstuff for selling items?
- Bags open check

tributing requires bags top be open. so added bag check.
- Tributestuff

* made this the same as sellstuff. so tribute will take either item on cursor or item name specified.

    /lootutils tributestuff
        Runs lootutils.tributeStuff() one time
- Launch arguments

made launch arguments for selling once and tributing once match the others.

/lua run lootnscoot sellstuff
/lua run lootnscoot tributestuff
- CleanupBags routine

added commands:
/lootutils cleanup
/lua run lootnscoot cleanup

both will run cleanupBags() function to destroy any items you may have in bags marked as destroy.

great for after marking an items destroy or manually looting things.

we don't automatically destroy items when marking them with /lootutils destroy because we may change our mind before cleaning out the bags.
- Merging functions.

the functions for cleanup sell tribute and bank were too similar and in some cases only changed by a single string value.

Combined them into one function loot.processItems(action)

now call

loot.processItems('Cleanup')  -- for cleanup
loot.processItems('Sell')     -- for selling
loot.processItems('Tribute')  -- for tributing
loot.processItems('Bank')     -- for banking

legacy calls still work but now call new function. 
left the legacy calls in for old integrations.

function loot.sellStuff()
    loot.processItems('Sell')
end

* added checks so we don't try to sell tribute or destroy items worn.
now we call 
mq.cmdf('/shift /itemnotify in pack%s %s leftmouseup', bag, slot) 
to make sure it is an item in the bagslots. because finditem would find augments inside of armor. 

* Added check for [GlobalItems] item settings. 
Items in [GlobalItems] section of Loot.ini should follow the same layout as other items 
itemname=action

/lootutils globalitem action itemname 
/lootutils globalitem action (ItemOnCurosr)
/lootutils globalitem quest itemname #

will add items to GlobalItems section
- Loot history gui

* Added output console for Looted Items.
   * outputs [Who] Looted [What]
   * Tries to grab item link using linkdb. will load plugin if not already loaded.
* /lootutils gui toggles console on of off.
- Loot_hist

removed a debug string and fixed some pop vars.
- Event noslow

* Added event for when item is to big for any inventory slots we now Warn and drop them to the ground.
- Loot_hist

update latest looted.
* adds reporting
- Merge pull request #6 from grimmier378/tribute_stuff

tribute_stuff
- Small cleanup, update changelog
- Merge pull request #7 from grimmier378/loot_hist

Loot hist
- Export menu

Exporting a menu to the looted console.
includes basic commands SellStuff, TributeStuff, Cleanup, Bank, and exit LootNScoot.

Added settings file settings for hide names, record data, and lookup links.

moved global loot check to after initial checks so we don't write the decision, unless its quest and we need to pull out the qty.
- Require adjustments

check for the looted module first.
if exists use that one as it may be more up to date.
else use our local copy 'loot_hist'

this way we don't have to update this everytime looted changes.

If for some reason neither are found. we disable looted plugin.
- Require adjustments

check for the looted module first.
if exists use that one as it may be more up to date.
else use our local copy 'loot_hist'

this way we don't have to update this everytime looted changes.

If for some reason neither are found. we disable looted plugin.
- Merge branch 'loot_hist' of https://github.com/grimmier378/lootnscoot into loot_hist
- Merge pull request #8 from grimmier378/loot_hist

some tweaks
- Actors instead of events
- Pass useactors

if we are using acrots. pass that to looted and we won't use link lookups with LinkDB since we are passing the link directly through actors.

Added toggles to the loot n scoot menu pushed to looted. to easily toggle settings on or off.
- Group_commands

added GroupChannel to settings.
this is the channel we use for group commands
like 'dgae' or 'bcca'  we will append a / as a prefix and
if its an EQBC command we append " /" to the end of it otherwise for dannet  leave it as is.

added the drop down to lootnscoot menu for group commands. so you can now send you entire group to sell, tribute, bank, or issue a cleanup on bags from the driver.
- Group_commands

added GroupChannel to settings.
this is the channel we use for group commands
like 'dgae' or 'bcca'  we will append a / as a prefix and
if its an EQBC command we append " /" to the end of it otherwise for dannet  leave it as is.

added the drop down to lootnscoot menu for group commands. so you can now send you entire group to sell, tribute, bank, or issue a cleanup on bags from the driver.
- Group_commands

typo on tributestuff
- Merge pull request #9 from grimmier378/actors_and_menu

pass useactors
- Merge pull request #10 from grimmier378/actions_overwrite

fix: overwriting action
- Toggle fix

fix for toggles only saving the autodestroy setting.
- Merge pull request #11 from grimmier378/toggle_save_fix

toggle fix
- Add LootNoDropNew option
- Pass Rule to looted

Pass the Evaluated Rule to Looted.
- Pass UseActors and Script Name

pass UseActors to guiLoot.UseActors
pass scripts name to guiLoot.caller
- Looted latest
- Forgot a branch.
- Adjusted the bags full check

made it so the check will check when there are mobs to loot and reset if you happened to clean space yourself.

Previously it wouldn't try to look at the corpse once set and would only reset if you used /lootutils sellstuff.
- Option to auto save sell decisions instead of keep
- Reload group command
- /lootutils restock option

save items you wish to buy per character in their settings ini section [BuyItems]
item name=qty

will attempt to buy any items in the list for that character, refilling anything up to the qty specified.

Kept this in the settings ini and not loot.ini so you can set it per character. my warrior doesn't need tiny daggers or malachite =)
- Commands /lootutils buy

/lootutils buy # will add item on cursor to list of buy items and set its qty.
/lootutils "item name" qty will set item name and qty in buy list when no item on cursor.
- Merge pull request #13 from grimmier378/pass_eval_value

Pass eval value
- Merge pull request #14 from grimmier378/fix_loot_hist

fix: removed theme stuff
- Merge pull request #15 from grimmier378/auto_restock

feat: auto restock option
- SQL update

Migrate loot.ini to a sql table.

Will use the sql table to keep track of global and normal loot rules. buy items are still stored in the char_settings file.

initial creation of the DB does not import the loot.ini file. this is to be done on one character with /lootutils import

All changes and new items will also be sent over actors to the other characters running on this PC so they stay current in their working tables.
- Oops accidently broke looted. fixed
- Missed a /rgl command in printout
- Batch the import instead of line by line

should alleviate some of the lag.
- Merge pull request #16 from grimmier378/convertion_to_SQL

SQL update
- Remove random Gearly logger abbreviation
- Merge pull request #17 from grimmier378/settings_and_loot_table_gui

feat: Settings and loot table GUI
- Bumping to ver 4

incase anyone had an RGMercs generated db but nuled it and not the update file.
- Noticed standalone being tagged without setting

Terminate was being read from the config and not reset when checking args at startup.
- Merge pull request #18 from grimmier378/fix_db_primary_key

Fix db primary key
- Merge pull request #19 from grimmier378/fix_db_primary_key

fix: Forgot Normal_Rules had Global 2x
<!-- generated by git-cliff -->
