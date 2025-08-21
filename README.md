# kirby-wacky-episode-WebPort
A Web Port of https://alexaugerdev-z.itch.io/kirby-wacky-episode

## How?
1. Get the Project Files via https://alexaugerdev-z.itch.io/kirby-wacky-episode and extract them.
2. Download & Install GameMaker Studio 2 LTS 2022.0.3.85, either via [Release Notes](https://gms.yoyogames.com/ReleaseNotes-LTS.html), or Direct Links for [Windows](https://gms.yoyogames.com/GameMaker-Installer-2022.0.3.85.exe) & [Mac](https://gms.yoyogames.com/GameMaker-2022.0.3.85.pkg).
3. Open the "Kirby - Wacky Episode.yyp" file in GameMaker Studio 2 LTS.
4. At your right, you will see the assets, go to the Objects, double click "obj_controller", double click Beginner Step, right click Call input_tick and delete it.
5. Click the target icon in top right, and change to: GX.games - VM (compiles faster and easier, used in my case) or YYC (runs faster but is harder/slower to compile) - Default - Default.
6. Save project, Run the game.
7. While the game is running in your browser, go to C:/Users/(Windows Username)/AppData/Local/GameMakerStudio2-LTS/GMS2TEMP (not sure what is it for Mac), then open the folder named (project name)_(some numbers)_(VC or YYC depending on what you chose) and copy everything in the /runner folder that's inside the previous folder to another, non-temporary location.
8. Now to run it, get [Python](https://www.python.org/downloads/), be sure to be in the directory with your copied runner.html and other files, then run `python -m http.server` and in your browser open `http://localhost:8000/runner.html`

## Why?
For pure educational purposes and fun, the fan-game offered project files already soo

## Credits
- Game & Project Files: https://alexaugerdev-z.itch.io/kirby-wacky-episode
- Some info about compiling for web port: https://burnedwebsite.vercel.app/guide/getting-started.html
