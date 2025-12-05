# How To Upload To Steam And Itch Using Command Line Tools

Go to the exports/ folder for your game

Create folder "export_tools"

Folder structure looks like this now:
```
	exports/
		export_tools/
		linux_standalone/
		linux_steam/
		mac_standalone/
		mac_steam/
		windows_standalone/
		windows_steam/
```
<img width="477" height="386" alt="directory layout" src="https://github.com/user-attachments/assets/f0806f82-dac9-43fa-8c17-7d214811ad08" />

_steam folders contain builds for steam, _standalone contain builds for itch


## STEAM SETUP
Download steam sdk: https://partner.steamgames.com/doc/sdk

Extract and navigate to sdk/tools/ContentBuilder/

Copy the contents of "ContentBuilder" folder into your "export_tools" folder

<img width="332" height="414" alt="export tools steam layout" src="https://github.com/user-attachments/assets/e19a2c62-785a-459e-9f59-d5acc5105f79" />

### Creating app and depot vdf files
Go to export_tools/scripts/

Create files with this format:
* app_build_<app_id>.vdf
* depot_build_<depot_id_1>.vdf
* depot_build_<depot_id_2>.vdf
* depot_build_<depot_id_3>.vdf

App id is the number found in the url of your game. For example, Half Life Alyx (https://store.steampowered.com/app/546560/HalfLife_Alyx/) has an app id of 546560. 

Depot ids are found on your depot settings on steamworks: https://partner.steamgames.com/apps/depots/<app_id>

You should have one depot_build vdf file for each depot and one overall app_build vdf file. I'm assuming you'll have 3 depots: Windows, Mac, and Linux.

Example file names using Half Life Alyx app id (I don't know what their depot ids actually are, just guessing):
* app_build_546560.vdf
* depot_build_546561.vdf
* depot_build_546562.vdf
* depot_build_546563.vdf

Here's my scripts folder for Endoparasitic 2:

<img width="570" height="312" alt="scripts folder" src="https://github.com/user-attachments/assets/4019160a-78d6-4cbb-9aa2-2b3dba501757" />

### App Build vdf File Contents Format
Copy this into your app_build vdf file and replace the parts in <> with your own.
```
	"appbuild"
	{
		"appid" "<app_id>"
		"desc" "build description here, can be changed in web after publishing also"
		// "setlive" "<beta_branch_name>" // optional, if you use beta branches this will automatically set them live on one, uncomment to use
		
		"contentroot" "..\content\" // content root folder, overridden in depot files
		"buildoutput" "..\output\" // logs output folder
		
		"verbose" "0" // set to 1 to get more info
		
		"depots"
		{
			"<depot_id_1>" "depot_build_<depot_id_1>.vdf"
			"<depot_id_2>" "depot_build_<depot_id_2>.vdf"
			"<depot_id_3>" "depot_build_<depot_id_3>.vdf"
		}
	}
```
Example app build vdf file contents using half life alyx app_id:
```
	"appbuild"
	{
		"appid" "546560"
		"desc" "Steam Cmd Build"
		// "setlive" "beta"
		
		"contentroot" "..\content\" 
		"buildoutput" "..\output\" 
		
		"verbose" "0" // set to 1 to get more info
		
		"depots"
		{
			"546561" "depot_build_546561.vdf"
			"546562" "depot_build_546562.vdf"
			"546563" "depot_build_546563.vdf"
		}
	}
```
Here's what mine looks like for Endoparasitic 2:

<img width="630" height="497" alt="app_build_contents" src="https://github.com/user-attachments/assets/1657c86d-1054-4314-b061-d753806379a7" />

### Depot Build vdf File Contents Format
```
	"DepotBuildConfig"
	{
		"DepotID" "<depot_id>"
		"contentroot" "<relative path to game build>"
		"FileMapping"
		{
			"LocalPath" "*"
			"DepotPath" "."
			"Recursive" "1"
	  }
	}
```
Example depot vdf file contents using Half Life Alyx probable depot_id:
```
	"DepotBuildConfig"
	{
		"DepotID" "546561"
		"contentroot" "..\..\windows_steam\"
		"FileMapping"
		{
			"LocalPath" "*"
			"DepotPath" "."
			"Recursive" "1"
	  }
	}
```
Here's what mine looks like for Endoparasitic 2:

<img width="500" height="300" alt="depot_build_1_contents" src="https://github.com/user-attachments/assets/7a1686fd-a808-4f2e-afd2-160463fe268f" />
<img width="500" height="300" alt="depot_build_2_contents" src="https://github.com/user-attachments/assets/45d2eb9d-8ea6-4c2f-86de-a2294487102d" />
<img width="500" height="300" alt="depot_build_3_contents" src="https://github.com/user-attachments/assets/477f7dd2-3116-49c1-815d-71a9d2877716" />


After making all the vdf files, go to export_tools/builder/

Open a terminal window here (on Windows click the path at top, type 'cmd' and press enter)

Enter "steamcmd.exe +login <steam_username>" (without quotes, and replace <steam_username> with your steam username)

Enter password

Enter Steam Guard code

Enter 'info' to verify you're logged in (optional)

You're now using SteamCmd and can upload games

Enter 'quit' to exit SteamCmd

Your steam login info is saved in a config now, you shouldn't have to enter it again

While in SteamCmd you can enter the following to upload your game:
```
run_app_build ..\scripts\app_build_<app_id>.vdf
```
(replace <app_id> with your app id, also note that the vdf file path is relative to steamcmd.exe, not your terminal working directory)

open the 'run_build.bat' file in export_tools/ in a text editor and update it to contain: 
```
builder\steamcmd.exe +login <steam_username> +run_app_build ..\scripts\app_build_<app_id>.vdf +quit
```
(replace <steam_username> and <app_id> with yours)

<img width="332" height="414" alt="pointing at run_build" src="https://github.com/user-attachments/assets/c90476a9-fa9c-49d9-8cf6-4f4049874df0" />

now you can double click the 'run_build.bat' file to automatically upload to steam

## ITCH SET UP

Download butler [https://itch.io/docs/butler/installing.html](https://itchio.itch.io/butler)

Copy butler.exe to export_tools/

Open terminal window (on Windows click path at top and type 'cmd')

This is the format for uploading builds to itch:
```
butler push <content path> <itch_username>/<game>:<channel>
```
Here's an example windows build of Endoparasitic 2 command for me:
```
butler push ../windows_standalone nartier/endoparasitic-2:windows
```
(I just enter that into the terminal and press enter)

Note that the game name needs to written exactly as it is in the url, for example my game has this url: https://nartier.itch.io/endoparasitic-2 so I made sure to use a '-' before the 2

You can replace the 'windows' at the end with whatever you want and a new downloadable category will automatically be created, e.g. you can do any of these:
```
butler push ../windows_standalone <itch_username>/<game>:windows-64
butler push ../windows_standalone_32 <itch_username>/<game>:windows-32
butler push ../mac_standalone <itch_username>/<game>:mac
butler push ../mac_standalone_intel <itch_username>/<game>:mac-intel
butler push ../linux_standalone <itch_username>/<game>:linux
butler push ../soundtrack <itch_username>/<game>:soundtrack
```
Now go to your 'run_build.bat' file from the steam set up (or create a new empty one) and add new lines for each channel on itch. Example bat file contents for me:
```
builder\steamcmd.exe +login deeprootinteractive +run_app_build ..\scripts\app_build_2990640.vdf +quit
butler push ../windows_standalone nartier/endoparasitic-2:windows
butler push ../mac_standalone nartier/endoparasitic-2:mac
butler push ../linux_standalone nartier/endoparasitic-2:linux
```
Now you can just double click your run_build.bat file to automatically publish new builds to steam and itch. I also made 2 more, one for only itch, and one for only steam.
