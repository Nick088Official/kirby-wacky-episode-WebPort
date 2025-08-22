# Kirby Wacky Episode - Web Port

A Web Port of the fan game by Alex Auger Dev: [alexaugerdev-z.itch.io/kirby-wacky-episode](https://alexaugerdev-z.itch.io/kirby-wacky-episode)

This repository contains the exported GX.Games builds and a complete guide on how to compile the project from the original source files, including a version with full mobile touch controls.

## Usage

**Note: It may take a few seconds or a minute to fully load without lag on some non powerful devices.**

There are two versions available to play:

-   **Standard Version (Keyboard Recommended):**
    https://nick088official.github.io/kirby-wacky-episode-WebPort/runner/runner.html

-   **Mobile Version (with Touch Controls):**
    https://nick088official.github.io/kirby-wacky-episode-WebPort/runnerMobile/runner.html

## How to Build from Source

This guide provides instructions for compiling both the standard web version and the version with added mobile touch controls.

### Prerequisites

1.  Get the Project Files from the original creator: [https://alexaugerdev-z.itch.io/kirby-wacky-episode](https://alexaugerdev-z.itch.io/kirby-wacky-episode) and extract them.
2.  Download & Install GameMaker Studio 2 LTS **2022.0.3.85**.
    *   Release Notes: [LTS Release Notes](https://gms.yoyogames.com/ReleaseNotes-LTS.html)
    *   Direct Link (Windows): [GameMaker-Installer-2022.0.3.85.exe](https://gms.yoyogames.com/GameMaker-Installer-2022.0.3.85.exe)
    *   Direct Link (Mac): [GameMaker-2022.0.3.85.pkg](https://gms.yoyogames.com/GameMaker-2022.0.3.85.pkg)
3.  Open the `Kirby - Wacky Episode.yyp` file in GameMaker Studio 2 LTS.

### Part A: Required Project Modification (For ALL Builds)

This step is necessary for both the standard and mobile builds to ensure the game runs correctly in a browser.

1.  In the Asset Browser, go to `Objects` -> `obj_controller`.
2.  Open the **Begin Step** Event.
3.  You will see an action called `Call input_tick`. Right-click this action and select **Delete**.

### Part B: Adding Mobile Touch Controls (Optional)

If you only want the standard desktop web port, you can **skip to Part C**. To create the mobile version, follow all of these steps. This process uses a single manager object to provide a startup menu where the user chooses their control scheme.

#### Step 1: Create the Font

1.  In the Asset Browser, right-click on the **Fonts** folder and select **Create Font**.
2.  Name the new font asset `fnt_virtual_pad`.
3.  In the font editor, choose a clear, bold font like **Arial** and set the **Size** to **18**.

#### Step 2: Create the Virtual Pad Manager Object

1.  Create a new object and name it `o_virtual_pad_manager`.
2.  Check the **Persistent** box.
3.  Add a **Create Event** and paste this code:
    ```gml
    selection_state = 0;
    global.is_mobile = false;
    var gui_w = display_get_gui_width(); var gui_h = display_get_gui_height(); var button_w = 400; var button_h = 100;
    pc_button_rect = [gui_w/2 - button_w/2, gui_h/2 - button_h - 20, button_w, button_h];
    mobile_button_rect = [gui_w/2 - button_w/2, gui_h/2 + 20, button_w, button_h];
    var margin = 15; var dpad_size = 50; var dpad_offset = 30; var dpad_center_x = 90; var dpad_center_y = gui_h - 90;
    up_button = [dpad_center_x-dpad_size/2, dpad_center_y-dpad_offset-dpad_size, dpad_size, dpad_size];
    down_button = [dpad_center_x-dpad_size/2, dpad_center_y+dpad_offset, dpad_size, dpad_size];
    left_button = [dpad_center_x-dpad_offset-dpad_size, dpad_center_y-dpad_size/2, dpad_size, dpad_size];
    right_button = [dpad_center_x+dpad_offset, dpad_center_y-dpad_size/2, dpad_size, dpad_size];
    var action_btn_w = 65; var action_btn_h = 45; var action_margin = 10;
    var row_base_y = gui_h-margin-action_btn_h; var row_base_x = gui_w-margin-action_btn_w;
    c_button = [row_base_x, row_base_y, action_btn_w, action_btn_h];
    x_button = [row_base_x-(action_btn_w+action_margin), row_base_y, action_btn_w, action_btn_h];
    z_button = [row_base_x-2*(action_btn_w+action_margin), row_base_y, action_btn_w, action_btn_h];
    space_button = [row_base_x-(action_btn_w+action_margin), row_base_y-(action_btn_h+action_margin), action_btn_w, action_btn_h];
    var sys_btn_w = 70; var sys_btn_h = 35; var sys_btn_x = gui_w-sys_btn_w-margin; var sys_btn_y_start = margin;
    enter_button = [sys_btn_x, sys_btn_y_start, sys_btn_w, sys_btn_h];
    shift_button = [sys_btn_x, sys_btn_y_start+(sys_btn_h+margin), sys_btn_w, sys_btn_h];
    alt_button = [sys_btn_x, sys_btn_y_start+2*(sys_btn_h+margin), sys_btn_w, sys_btn_h];
    up_pressed=false; down_pressed=false; left_pressed=false; right_pressed=false; z_pressed=false; x_pressed=false; c_pressed=false; space_pressed=false; alt_pressed=false; shift_pressed=false; enter_pressed=false;
    was_up_pressed=false; was_down_pressed=false; was_left_pressed=false; was_right_pressed=false; was_z_pressed=false; was_x_pressed=false; was_c_pressed=false; was_space_pressed=false; was_alt_pressed=false; was_shift_pressed=false; was_enter_pressed=false;
    img_spd = 0; // Compatibility variable for the pause menu
    ```
4.  Add an **End Step Event** and paste this code:
    ```gml
    if (selection_state == 0) {
        keyboard_clear(vk_anykey);
        if (device_mouse_check_button_pressed(0, mb_left)) {
            var _mx = device_mouse_x_to_gui(0); var _my = device_mouse_y_to_gui(0);
            var b = pc_button_rect; if (point_in_rectangle(_mx, _my, b[0], b[1], b[0]+b[2], b[1]+b[3])) { global.is_mobile = false; selection_state = 1; }
            b = mobile_button_rect; if (point_in_rectangle(_mx, _my, b[0], b[1], b[0]+b[2], b[1]+b[3])) { global.is_mobile = true; selection_state = 1; }
        }
    } else {
        if (!global.is_mobile) exit;
        var is_up=false, is_down=false, is_left=false, is_right=false, is_z=false, is_x=false, is_c=false, is_space=false, is_alt=false, is_shift=false, is_enter=false;
        for (var i=0; i<5; i++) { if (device_mouse_check_button(i, mb_left)) {
            var touch_x=device_mouse_x_to_gui(i); var touch_y=device_mouse_y_to_gui(i); var b;
            b=up_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_up=true;
            b=down_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_down=true;
            b=left_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_left=true;
            b=right_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_right=true;
            b=z_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_z=true;
            b=x_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_x=true;
            b=c_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_c=true;
            b=space_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_space=true;
            b=alt_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_alt=true;
            b=shift_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_shift=true;
            b=enter_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_enter=true;
        }}
        up_pressed=is_up; down_pressed=is_down; left_pressed=is_left; right_pressed=is_right; z_pressed=is_z; x_pressed=is_x; c_pressed=is_c; space_pressed=is_space; alt_pressed=is_alt; shift_pressed=is_shift; enter_pressed=is_enter;
        function simulate_key(is_pressed, was_pressed, key) { if (is_pressed and !was_pressed) { keyboard_key_press(key); } if (!is_pressed and was_pressed) { keyboard_key_release(key); } return is_pressed; }
        was_up_pressed=simulate_key(up_pressed,was_up_pressed,vk_up); was_down_pressed=simulate_key(down_pressed,was_down_pressed,vk_down); was_left_pressed=simulate_key(left_pressed,was_left_pressed,vk_left); was_right_pressed=simulate_key(right_pressed,was_right_pressed,vk_right);
        was_z_pressed=simulate_key(z_pressed,was_z_pressed,ord("Z")); was_x_pressed=simulate_key(x_pressed,was_x_pressed,ord("X")); was_c_pressed=simulate_key(c_pressed,was_c_pressed,ord("C")); was_space_pressed=simulate_key(space_pressed,was_space_pressed,vk_space);
        was_alt_pressed=simulate_key(alt_pressed,was_alt_pressed,vk_alt); was_shift_pressed=simulate_key(shift_pressed,was_shift_pressed,vk_shift); was_enter_pressed=simulate_key(enter_pressed,was_enter_pressed,vk_enter);
    }
    ```
5.  Add a **Draw GUI Event** and paste this code:
    ```gml
    if (selection_state == 0) {
        draw_set_color(c_black); draw_set_alpha(0.7); draw_rectangle(0, 0, display_get_gui_width(), display_get_gui_height(), false); draw_set_alpha(1.0);
        draw_set_font(fnt_virtual_pad); draw_set_halign(fa_center); draw_set_valign(fa_middle);
        var b = pc_button_rect; draw_set_color(make_color_rgb(100, 100, 100)); draw_rectangle(b[0], b[1], b[0]+b[2], b[1]+b[3], false);
        draw_set_color(c_white); draw_text(b[0]+b[2]/2, b[1]+b[3]/2, "PC (Keyboard)");
        b = mobile_button_rect; draw_set_color(make_color_rgb(100, 100, 100)); draw_rectangle(b[0], b[1], b[0]+b[2], b[1]+b[3], false);
        draw_set_color(c_white); draw_text(b[0]+b[2]/2, b[1]+b[3]/2, "Mobile (Touch Controls)");
    } else {
        if (!global.is_mobile) exit;
        draw_set_alpha(0.5);
        var col = up_pressed ? c_yellow : c_white; draw_set_color(col); var b = up_button; draw_triangle(b[0]+b[2]/2, b[1], b[0], b[1]+b[3], b[0]+b[2], b[1]+b[3], false);
        col = down_pressed ? c_yellow : c_white; draw_set_color(col); b = down_button; draw_triangle(b[0], b[1], b[0]+b[2], b[1], b[0]+b[2]/2, b[1]+b[3], false);
        col = left_pressed ? c_yellow : c_white; draw_set_color(col); b = left_button; draw_triangle(b[0]+b[2], b[1], b[0], b[1]+b[3]/2, b[0]+b[2], b[1]+b[3], false);
        col = right_pressed ? c_yellow : c_white; draw_set_color(col); b = right_button; draw_triangle(b[0], b[1], b[0]+b[2], b[1]+b[3]/2, b[0], b[1]+b[3], false);
        draw_set_halign(fa_center); draw_set_valign(fa_middle); draw_set_font(fnt_virtual_pad);
        function draw_rect_button(button_rect, text, is_pressed) {
            var col = is_pressed ? c_yellow : c_white; draw_set_color(col); draw_rectangle(button_rect[0], button_rect[1], button_rect[0] + button_rect[2], button_rect[1] + button_rect[3], false);
            var _x = button_rect[0] + button_rect[2]/2; var _y = button_rect[1] + button_rect[3]/2;
            draw_set_color(c_black); draw_text(_x-1, _y, text); draw_text(_x+1, _y, text); draw_text(_x, _y-1, text); draw_text(_x, _y+1, text);
            draw_set_color(c_white); draw_text(_x, _y, text);
        }
        draw_rect_button(z_button, "Z", z_pressed); draw_rect_button(x_button, "X", x_pressed); draw_rect_button(c_button, "C", c_pressed);
        draw_rect_button(space_button, "SPACE", space_pressed); draw_rect_button(enter_button, "ENTER", enter_pressed);
        draw_rect_button(shift_button, "SHIFT", shift_pressed); draw_rect_button(alt_button, "ALT", alt_pressed);
        draw_set_alpha(1); draw_set_color(c_white); draw_set_halign(fa_left); draw_set_valign(fa_top);
    }
    ```

#### Step 5: Place the Object in the First Room

1.  In the Asset Browser, find your **first room**, Room_title.
2.  Place **one** instance of the new `o_virtual_pad_manager` object anywhere in this room.

### Part C: Compiling and Exporting

1.  Click the target icon in the top right and change to: **GX.games - VM** (This compiles faster and is easier for this purpose; you could also try YYC for a bit better native performance but harder to compile)..
2.  Save the project, then click the **Run** (play) button.
3.  While the game is running, navigate to the GameMaker temporary folder.
    -   **Windows:** `C:/Users/(Your Username)/AppData/Local/GameMakerStudio2-LTS/GMS2TEMP`
    -   **Mac:** Open Finder, go to the "Go" menu, hold Option, click "Library", then navigate to `Application Support/GameMakerStudio2-LTS/GMS2TEMP`.
4.  Open the folder named something like `Kirby - Wacky Episode_..._VM`.
5.  Inside, you will find a `runner` folder. **Copy the contents** of this folder.
6.  To test locally, get [Python](https://www.python.org/downloads/). Open a command prompt in the directory with your copied files (e.g., in `/runner`) and run `python -m http.server`. Then open `http://localhost:8000/runner.html` in your browser.

## Why?

For pure educational purposes, fun, and to make this excellent fan-game more accessible on mobile devices. The project files were generously provided by the original creator.

## Credits

-   **Game & Project Files:** [Alex Auger Dev](https://alexaugerdev-z.itch.io/kirby-wacky-episode)
-   **GameMaker Studio 2 Web Porting Info:** [burnedwebsite.vercel.app](https://burnedwebsite.vercel.app/guide/getting-started.html)