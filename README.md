# Kirby Wacky Episode - Web Port

A Web Port of the fan game by Alex Auger Dev: [alexaugerdev-z.itch.io/kirby-wacky-episode](https://alexaugerdev-z.itch.io/kirby-wacky-episode)

This repository contains the exported Web GX.games builds and a complete guide on how to compile the project from the original source files, with an optional patch for mobile touch controls.

## Usage

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

### Part A: Required Project Modification

This step is necessary for **both** the standard and mobile builds.

1.  In the Asset Browser, go to `Objects` -> `obj_controller`.
2.  Open the **Begin Step** Event.
3.  You will see an action called `Call input_tick`. Right-click this action and select **Delete**. This is done to prevent conflicts with how the engine handles input polling for web builds.

### Part B: Adding Mobile Touch Controls (Optional)

If you only want the standard desktop web port, you can **skip to Part C**. To create the mobile version, follow all of these steps.

#### Step 1: Create the Font

1.  In the Asset Browser, right-click on the **Fonts** folder and select **Create Font**.
2.  Name the new font asset `fnt_virtual_pad`.
3.  In the font editor, choose a clear, bold font like **Arial** and set the **Size** to **18**.

#### Step 2: Create the Mobile Check Object

1.  Create a new object and name it `o_mobile_check`.
2.  Check the **Persistent** box.
3.  Add a **Game Start Event** (`Add Event` -> `Other` -> `Game Start`).
4.  In the Game Start Event, add this code:
    ```gml
    global.is_mobile = false;
    if (os_browser == browser_not_a_browser) {
        if (os_type == os_android or os_type == os_ios) global.is_mobile = true;
    } else {
        var user_agent = string_lower(browser_get_user_agent());
        if (string_pos("mobile", user_agent) > 0 or string_pos("android", user_agent) > 0 or string_pos("iphone", user_agent) > 0) {
            global.is_mobile = true;
        }
    }
    ```

#### Step 3: Create the Virtual Gamepad GUI Object

1.  Create a new object and name it `o_virtual_pad_gui`.
2.  Check the **Persistent** box.
3.  Add a **Create Event** and paste this code:
    ```gml
    var gui_w = display_get_gui_width(); var gui_h = display_get_gui_height(); var margin = 15;
    var dpad_size = 50; var dpad_offset = 30; var dpad_center_x = 90; var dpad_center_y = gui_h - 90;
    up_button = [dpad_center_x - dpad_size/2, dpad_center_y - dpad_offset - dpad_size, dpad_size, dpad_size];
    down_button = [dpad_center_x - dpad_size/2, dpad_center_y + dpad_offset, dpad_size, dpad_size];
    left_button = [dpad_center_x - dpad_offset - dpad_size, dpad_center_y - dpad_size/2, dpad_size, dpad_size];
    right_button = [dpad_center_x + dpad_offset, dpad_center_y - dpad_size/2, dpad_size, dpad_size];
    var action_btn_w = 65; var action_btn_h = 45; var action_margin = 10;
    var row_base_y = gui_h - margin - action_btn_h; var row_base_x = gui_w - margin - action_btn_w;
    c_button = [row_base_x, row_base_y, action_btn_w, action_btn_h];
    x_button = [row_base_x - (action_btn_w + action_margin), row_base_y, action_btn_w, action_btn_h];
    z_button = [row_base_x - 2 * (action_btn_w + action_margin), row_base_y, action_btn_w, action_btn_h];
    space_button = [row_base_x - (action_btn_w + action_margin), row_base_y - (action_btn_h + action_margin), action_btn_w, action_btn_h];
    var sys_btn_w = 70; var sys_btn_h = 35; var sys_btn_x = gui_w - sys_btn_w - margin; var sys_btn_y_start = margin;
    enter_button = [sys_btn_x, sys_btn_y_start, sys_btn_w, sys_btn_h];
    shift_button = [sys_btn_x, sys_btn_y_start + (sys_btn_h + margin), sys_btn_w, sys_btn_h];
    alt_button = [sys_btn_x, sys_btn_y_start + 2 * (sys_btn_h + margin), sys_btn_w, sys_btn_h];
    up_pressed = false; down_pressed = false; left_pressed = false; right_pressed = false; z_pressed = false; x_pressed = false; c_pressed = false; space_pressed = false; alt_pressed = false; shift_pressed = false; enter_pressed = false;
    ```
4.  Add a **Draw GUI Event** and paste this code:
    ```gml
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
    ```

#### Step 4: Create the Virtual Gamepad Logic Object

1.  Create a new object and name it `o_virtual_pad_logic`.
2.  Check the **Persistent** box.
3.  Add a **Create Event** and paste this code:
    ```gml
    was_up_pressed=false; was_down_pressed=false; was_left_pressed=false; was_right_pressed=false; was_z_pressed=false; was_x_pressed=false; was_c_pressed=false; was_space_pressed=false; was_alt_pressed=false; was_shift_pressed=false; was_enter_pressed=false;
    ```
4.  Add an **End Step Event** and paste this code:
    ```gml
    if (!global.is_mobile) exit; if (!instance_exists(o_virtual_pad_gui)) exit;
    var is_up_pressed=false, is_down_pressed=false, is_left_pressed=false, is_right_pressed=false, is_z_pressed=false, is_x_pressed=false, is_c_pressed=false, is_space_pressed=false, is_alt_pressed=false, is_shift_pressed=false, is_enter_pressed=false;
    for (var i=0; i<5; i++) { if (device_mouse_check_button(i, mb_left)) {
        var touch_x=device_mouse_x_to_gui(i); var touch_y=device_mouse_y_to_gui(i); var b;
        b=o_virtual_pad_gui.up_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_up_pressed=true;
        b=o_virtual_pad_gui.down_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_down_pressed=true;
        b=o_virtual_pad_gui.left_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_left_pressed=true;
        b=o_virtual_pad_gui.right_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_right_pressed=true;
        b=o_virtual_pad_gui.z_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_z_pressed=true;
        b=o_virtual_pad_gui.x_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_x_pressed=true;
        b=o_virtual_pad_gui.c_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_c_pressed=true;
        b=o_virtual_pad_gui.space_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_space_pressed=true;
        b=o_virtual_pad_gui.alt_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_alt_pressed=true;
        b=o_virtual_pad_gui.shift_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_shift_pressed=true;
        b=o_virtual_pad_gui.enter_button; if (point_in_rectangle(touch_x,touch_y,b[0],b[1],b[0]+b[2],b[1]+b[3])) is_enter_pressed=true;
    }}
    o_virtual_pad_gui.up_pressed=is_up_pressed; o_virtual_pad_gui.down_pressed=is_down_pressed; o_virtual_pad_gui.left_pressed=is_left_pressed; o_virtual_pad_gui.right_pressed=is_right_pressed;
    o_virtual_pad_gui.z_pressed=is_z_pressed; o_virtual_pad_gui.x_pressed=is_x_pressed; o_virtual_pad_gui.c_pressed=is_c_pressed; o_virtual_pad_gui.space_pressed=is_space_pressed;
    o_virtual_pad_gui.alt_pressed=is_alt_pressed; o_virtual_pad_gui.shift_pressed=is_shift_pressed; o_virtual_pad_gui.enter_pressed=is_enter_pressed;
    function simulate_key(is_pressed, was_pressed, key) { if (is_pressed and !was_pressed) { keyboard_key_press(key); } if (!is_pressed and was_pressed) { keyboard_key_release(key); } return is_pressed; }
    was_up_pressed=simulate_key(is_up_pressed,was_up_pressed,vk_up); was_down_pressed=simulate_key(is_down_pressed,was_down_pressed,vk_down); was_left_pressed=simulate_key(is_left_pressed,was_left_pressed,vk_left); was_right_pressed=simulate_key(is_right_pressed,was_right_pressed,vk_right);
    was_z_pressed=simulate_key(is_z_pressed,was_z_pressed,ord("Z")); was_x_pressed=simulate_key(is_x_pressed,was_x_pressed,ord("X")); was_c_pressed=simulate_key(is_c_pressed,was_c_pressed,ord("C")); was_space_pressed=simulate_key(is_space_pressed,was_space_pressed,vk_space);
    was_alt_pressed=simulate_key(is_alt_pressed,was_alt_pressed,vk_alt); was_shift_pressed=simulate_key(is_shift_pressed,was_shift_pressed,vk_shift); was_enter_pressed=simulate_key(is_enter_pressed,was_enter_pressed,vk_enter);
    ```

#### Step 5: Place the Objects in the First Room

1.  In the Asset Browser, find your **first room** (e.g., `r_logo` or `r_title`).
2.  Place **one** instance of `o_mobile_check`, **one** instance of `o_virtual_pad_gui`, and **one** instance of `o_virtual_pad_logic` anywhere in this room.

### Part C: Compiling and Exporting

1.  Click the target icon in the top right and change to: **GX.games - VM**. (This compiles faster and is easier for this purpose; you could also try YYC for a bit better native performance but harder to compile).
2.  Save the project, then click the **Run** (play) button.
3.  While the game is running in your browser, navigate to the GameMaker temporary folder.
    -   **Windows:** `C:/Users/(Your Username)/AppData/Local/GameMakerStudio2-LTS/GMS2TEMP`
    -   **Mac:** Open Finder, click "Go" in the menu bar, hold the Option key, and click "Library". Then navigate to `Application Support/GameMakerStudio2-LTS/GMS2TEMP`.
4.  Open the folder named something like `Kirby - Wacky Episode_..._VM`.
5.  Inside, you will find a `runner` folder. **Copy the contents** of this folder.
6.  To test locally, get [Python](https://www.python.org/downloads/). Open a command prompt or terminal in the directory with your copied files (e.g., in `/runner`) and run `python -m http.server`. Then open `http://localhost:8000/runel.html` in your browser.

## Why?

For pure educational purposes, fun, and to make this excellent fan-game more accessible on mobile devices. The project files were generously provided by the original creator.

## Credits

-   **Game & Project Files:** [Alex Auger Dev](https://alexaugerdev-z.itch.io/kirby-wacky-episode)
-   **GameMaker Studio 2 Web Port Info:** [burnedwebsite.vercel.app](https://burnedwebsite.vercel.app/guide/getting-started.html)