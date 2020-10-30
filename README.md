# ![Godot](https://raw.githubusercontent.com/hlfstr/gdnative-project/master/icon.png) GDUtil

A collection of useful objects written in GDNative.

Meant to be used with: [GDNative Project](https://github.com/hlfstr/gdnative-project)

## Usage

Create an "addons" directory in your project's folder.  

Pull this into your project by using a git subtree:
````bash
git subtree add --prefix addons/gdutil https://github.com/hlfstr/gdutil.git master --squash
````

If you would rather use a submodule, that is fine.  But, having a subtree means your local changes are committed to your project as well.

## Tools

#### G3DCam

Basic First-Person 3D camera for traversing the environment. 
- Toggleable collision detection
- Configurable controls

**Usage:** Add a `Camera` to your scene and ensure that `Camera/Current` is enabled.  Add the `G3DCam.gdns` file to your camera and configure the options.

#### GDispaly

Simple class that destroys itself on start if not a debug build.
- Useful for items you want to show only when debugging
    - Version numbers on-screen, FPS displays, logos, etc

**Usage:** Create a new scene of type `CanvasLayer` and add any children you want to display. Add the `GDisplay.gdns` script to your `CanvasLayer`.
- Change the `CanvasLayer/Layer` property to something higher to always be on top
- Recommend your first child be a `Control` to set screen bounding
- Ensure you check the `Control/Mouse/Filter` property on any children of your `CanvasLayer`
    - To be non-interactive, set `Control/Mouse/Filter` to be `Ignore` 
- Personally, I set the created scene as an _Autoload_ to have it easily persist between scenes

#### GSceneManager

Class for handling load screens, and changing between scenes.
- Required to be set as an _Autoload_

**Usage:** Create a new scene of type `CanvasLayer` and add the `GSceneManager.gdns` script to it.  Save, and set as an _Autoload_.  Meant to have 2 children, your FadeOut/FadeIn animation, and your Loading Screen; neither are required.  

To use the Fade Out/Fade In and/or the Loading Screen features, create a `Control` as a child of `GSceneManager`, one for each.  Add an `AnimationPlayer` as a child of your `Control`.  Set the property on `GSceneManager` for `GSceneManager/Load Animator Path` and/or `GSceneManager/Fade Animator Path` to be the relative path to the `AnimationPlayer`. For example: `Control/AnimationPlayer`.  If the path is left empty the feature will be disabled.

**Signals:**
- fade_in
    - Called when fade in is completed
    - Parameter: callback (int) - To allow for state-awareness of the fade
    - Ex: `_onFadeIn(callback : int)`
- fade_out 
    - Called when fade out is completed
    - Parameter: callback (int) - To allow for state-awareness of the fade
    - Ex: `_onFadeOut(callback : int)`
- load_start
    - !TBD
- load_end
    - !TBD
- quit
    - Called when the game is exiting via GSceneManager
    - This is meant for last-minute changes, you cannot stop the quit
    - You should also not run quit in this signal

**Functions:**
- `GSceneManager::instance()`
    - C++ only!
    - Static instance of the class, can be used to run the following as well
- `FadeIn(String name, float speed, int callback)`
    - Starts fade in animation
    - **Params:**
        - name     -- Name of animation
        - speed    -- Speed of animation
        - callback -- int value sent back via signal
    - C++: `void fadeIn(godot::String name, real_t speed, int callback)`
- `FadeOut(String name, float speed, int callback)`
    - Starts fade out animation
    - **Params:**
        - name     -- Name of animation
        - speed    -- Speed of animation
        - callback -- int value sent back via signal
    - C++: `void fadeOut(godot::String name, real_t speed, int callback)`
- `Quit()`
    - Calls `quit` signal and then exits
    - C++:  `static void GSceneManager::Quit()`
- `ChangeScene(Resource scene)`
    - Changes the active scene to passed in scene
    - **Params:**
        - scene -- Resource of scene, using `load()` or `preload()`
    - C++: `void changeScene(Resource *res)`

#### GSettings
- Recommended to be set as an _Autoload_
- Easy storage for user settings
- Control Binder built-in

**Usage:** Create a `Node` and place the `GSettings.gdns` script on it.Set the settings `GSettings/Filename` property.

- Set as an _Autoload_ for easy calling in GDScript
- Recommend going to Project Settings `Application/Config`:
    - Enable `Use Custom User Dir`
    - Set `Custom User Dir Name`

`GSettings` requires a GDScript to provide the needed settings, and the easiest way is to create a child `Node` on your `GSettings` node.  Attach a GDScript to this new `Node`.

Settings are organized by Section (ex: Display, Audio, etc). 
Each Section has Keys that correspond to a setting Value.

An example GDScript to define settings:
````python
# Define our Default Settings
var _defaultSettings := {
    # Section: display
    "display": {
        # Key: winWidth | Value: 1920
        "winWidth": 1920,
        # Key: winHeight | Value: 1080
        "winHeight": 1080
    },
    # Section: music
    "music": {
        # Key: volume | Value: 25
        "volume": 25,
        # Key: enabled | Value: true
        "enabled": true
    }
}

# The following will pass our _defaultSettings to GSettings
var _ready():
    # We have to defer the call, as the parent GSettings is not ready
    get_parent().call_deferred("SetDefaultSettings", _defaultSettings)
````

# WIP
