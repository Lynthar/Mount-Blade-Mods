# Manual Installation Instructions

1. Export the modules to Mount & Blade II Bannerlord\Modules

> Remember to remove previous installation fully and do not overwrite.
> Requires MCMv4 to view settings.

# Quick Console Commands

## captivity.reload_settings
Reloads the settings in game, if not using MCMv4.
Format: `captivity.reload_settings`

## captivity.force_fire_event [EventName] [?CaptiveName]
Attempts to force fire event, bypassing conditions. Looks in categories of (random, captor) if free and (captive) if prisoner.
Format: `captivity.force_fire_event [EventName] [CaptiveName]`

## captivity.fire_event [EventName] [?CaptiveName]
Attempts to fire event (respects conditions). Looks in categories of (random, captor) if free and (captive) if prisoner.
Format: `captivity.fire_event [EventName] [CaptiveName]`

## captivity.can_i_run_this_event [EventName] [?CaptiveName]
Tests if an event can be run without actually firing it.
Format: `captivity.can_i_run_this_event [EventName] [CaptiveName]`

## captivity.list_events [?SEARCH_TERM]
Lists currently loaded events.
Format: `captivity.list_events [SEARCH_TERM]`

## captivity.list_possible_events [?SEARCH_TERM]
Lists events that can currently be run based on your current game state.
Format: `captivity.list_possible_events [SEARCH_TERM]`

## captivity.list_images [?SEARCH_TERM]
Lists all loaded event images.
Format: `captivity.list_images [SEARCH_TERM]`

## captivity.impregnate [?HERO]
Impregnates specified hero (or main hero if not specified).
Format: `captivity.impregnate [HERO]`

## captivity.ImpregnateBy [HERO] [HERO]
Impregnates target hero by specified hero.
Format: `captivity.ImpregnateBy [TargetName] [FromName]`

## captivity.current_location_flags [?SEARCH_HERO]
Shows location flags for specified hero (or main hero if not specified).
Format: `captivity.current_location_flags [SEARCH_HERO]`

## captivity.current_status [?SEARCH_HERO]
Lists Hero/Party's Captivity Events related status.
Format: `captivity.current_status [SEARCH_HERO]`

## captivity.debug_status
Check the status of notifications, pregnancies, and equipment returns.
Format: `captivity.debug_status`

## captivity.reset_status [?SEARCH_HERO]
Resets the Hero's Captivity Events related status (prostitution, slavery, skills).
Format: `captivity.reset_status [SEARCH_HERO]`

## captivity.clear_pregnancies
Clears all pregnancies held by Captivity Events.
Format: `captivity.clear_pregnancies`

## captivity.clean_save
Attempts to clean save of Captivity Events related data (pregnancies, brothels, parties, resets status).
Format: `captivity.clean_save`

## captivity.fire_fix
DEV use only, Custom Fix for raft state, children, and brothel initialization.
Format: `captivity.fire_fix`

## captivity.reload_images
Reloads the event images in game.
Format: `captivity.reload_images`

## captivity.reload_events
Reloads the events and images (live editing) in game.
Format: `captivity.reload_events`

## captivity.clear_parties
Clears custom CE parties from the game.
Format: `captivity.clear_parties [PARTY_ID]`

## captivity.play_sound [SOUND_ID]
Plays specific sound via sound_id.
Format: `captivity.play_sound [SOUND_ID]`

## captivity.create_new_prisoner [?CULTURE_NAME] [?GENDER] [?IS_HERO]
Creates a new prisoner and adds to your party.
- CULTURE_NAME: Name or ID of culture (default: player's culture)
- GENDER: 'male' or 'female' (default: female)
- IS_HERO: 'true' or 'false' (default: true)
Format: `captivity.create_new_prisoner [CULTURE_NAME] [GENDER] [IS_HERO]`


# Game Crashing 
If you are experiencing crashes on startup please remember to unblock all dlls in \zCaptivityEvents\bin\Win64_Shipping_Client\.


# Settings

## Main Settings
Instructions of various settings when using manual (No-MCMv4). Settings is located in Modules\zCaptivityEvents\ModuleLoader\CaptivityRequired\Events\CESettings.xml.

### EventCaptiveOn
Turn on Captive Events
### EventOccurrenceOther - Event wait between occurances in Traveling Party
How often should an event occur while in a regular party. (Gametime in between events)
### EventOccurrenceSettlement - Event wait between occurances in Settlement
How should an event occur in settlements. (Prostitution affected too) (Gametime in between events)
### EventOccurrenceLord - Event wait between occurances in Lord's Party
How often should an event occur in a lord's party. (Gametime in between events)

### EventCaptorOn
Turn on Captor Events
### EventOccurrenceCaptor - Event wait between occurances while Captor
How often should an event occur while Captor. (Gametime in between events)
### EventCaptorDialogue - Prisoner Dialogue
Overwrites the default prisoner conversation menu.
### EventCaptorNotifications - Event Map Notifications
If events will fire as map notifications for captor/random.
### EventCaptorCustomTextureNotifications - Notifications Textures
Default Notifications textures are replaced by a custom texture notifications.
### EventRandomEnabled - Random Events Enabled
Random events are events that do not require captives.
### EventRandomFireChance - Random Events Ratio
If captives in party how often should random events fire over captor.
### EventCaptorGearCaptives - Captives Gear (Captor)
Captive Heroes who have been stripped gain their gear back after escape.
### EventProstituteGear - Toggle Brothel Prisoner's Clothing (Hero)
Changes the brothel prisoner's clothing to the settlement's culture.
### HuntLetPrisonersEscape - Allow escape during hunt
Allows prisoners to escape if not killed or wounded in the hunt
### HuntBegins - Hunting begins time after mission load
Seconds to wait until hunt begins
### AmountOfTroopsForHunt - Max amount of prisoners to spawn for hunt
Amount of prisoners that will spawn for hunt

### PrisonerEscapeBehavior - Modified Prisoner Escape Behavior
Use modified behaviour in game for prisoner escape, Turn off for compatability with mods that effect prisoner behavior.
### PrisonerHeroEscapeChanceParty - Hero Prisoner Chance (Party) 
Hero prisoner daily escape chance from player's party, -1 means use regular calculation 
### PrisonerHeroEscapeChanceSettlement - Hero Prisoner Chance (Settlement) 
Hero prisoner daily escape chance from player's settlements, -1 means use regular calculation 
### PrisonerHeroEscapeChanceOther - Hero Prisoner Chance (Other) 
Hero prisoner daily escape chance from non-player sources, -1 means use regular calculation 
### PrisonerNonHeroEscapeChanceParty - Regular Prisoner Escape Chance (Party) 
Regular prisoner escape chance from player's party, -1 means use regular calculation, active only when prisoner exceeded
### PrisonerNonHeroEscapeChanceSettlement - Regular Prisoner Escape Chance (Settlement)
Regular prisoner escape chance from player's settlements, -1 means use regular calculation, active only when prisoner exceeded
### PrisonerNonHeroEscapeChanceOther - Regular Prisoner Escape Chance (Others)
Regular prisoner escape chance from non-player sources, -1 means use regular calculation, active only when prisoner exceeded
### EscapeAutoRansom - Games Default Auto Ransom Behavior
Allow the games default behaviour regarding auto-ransom. Depends on Modified Prisoner Escape Behavior.
- 0 - Off
- 1 - Disabled For Player
- 2 - On
### PrisonerExceeded - Games Default Exceeded Prisoners System
Allows the games default behaviour regarding exceeded prisoner system, Hourly escape chance based on default 10% or above chances

### BrothelOption - Brothel Prisoners Allowed
Allows the gender to be prisoners in the brothel
- 0 - Any
- 1 - Female
- 2 - Male

### NonSexualContent - Non Sexual Content
Should non sexual content events be enabled.

### SexualContent - Sexual Content
Should sexual content events be enabled.

### CommonControl - Common Events
Should events tagged with common be enabled.

### ProstitutionControl - Prostitution Events 
Should Prostitution events be enabled, also controls brothel 
### SlaveryToggle - Slavery Events 
Should Slavery events be enabled. 
### FemdomControl - Femdom Events 
Should Female Domination events be enabled. 
### RomanceControl - Romance Events 
Should Romance events be enabled. 
### StolenGear - Stolen Gear 
Should the captor take the player's gear. 
### StolenGearQuest - Stolen Gear Quest 
Should quest activate to retrieve stolen gear. 
### StolenGearDuration - Stolen Gear Quest Duration 
How long should stolen gear quest last. 
### StolenGearChance - Stolen Gear Quest Chance 
Chance someone finds your stolen gear. 
### BetterOutFitChance - Better OutFit Chance 
Likelyhood of receiving a better outfit (Given based on captors culture). 
### WeaponChance - Weapon Chance 
Likelyhood of receiving an weapon. 
### WeaponBetterChance - Better Weapon Chance 
Likelyhood of receiving an better weapon. 
### WeaponSkill - Weapon Skill Calculation (Ignores Chance) 
(One Handed, Two Handed, Polearm Skill) is calculated for the player to receive a better weapon or not, ignores chance. 
### RangedBetterChance - Ranged Weapon Chance 
Likelyhood of receiving an better ranged weapons. 
### RangedSkill - Ranged Calculation (Ignores Chance) 
(Bow, Crossbow, Throwing) is calculated for the player to receive an one-handed weapon or not, ignores chance. 
### HorseChance - Horse Chance 
Likelyhood of receiving an horse. 
### HorseSkill - Horse Skill Calculation (Ignores Chance) 
Horse Skill is calculates if the player receives a horse or not, ignores chance. 
### PregnancyChance - Pregnancy Chance 
Likelyhood of impregnation, overrides various events 
### AttractivenessSkill - Attractiveness Calculation (Ignores Chance) 
Perks (Perfect Health, Gourmet, InBloom) and Charm level calculates impregnation chance, ignores chance. 
### UsePregnancyModifiers - Use PregnancyModifiers 
Use event pregnancy modifiers on top of the current chance or calculation. 
### PregnancyDurationInDays - Pregnancy Duration In Days 
Days pregnancy lasts. 
### PregnancyToggle - Pregnancy Toggle 
Allows impregnation by the mod. 
### PregnancyMessages - Pregnancy Messages 
Allows daily pregnancy messages by Captivity Events. 

### RenownChoice - Renown Choice
Renown decrease/increase choice. Keeps minimum at current clan level or allows to decrease or disables renown changes.
- 0 - Off
- 1 - Decrease/Increase Clan Level
- 2 - Keep/Increase Clan Level

### RenownMin - Renown Min (Enabled On Decrease/Increase Clan Level)
Renown can only drop to this point. Enabled if Clan Choice is Decrease/Increase Clan Level.

### LogToggle - Logging Toggle (Slows Down The Game)
Log the events (Debug Mode)

### CustomBackgrounds - Custom Backgrounds
Custom backgrounds toggle. (Will not help if default backgrounds are overwritten)