+++
title = 'Installer Hyprland sur une distribution Arch Linux'
date = 2024-05-03T18:19:28+02:00
draft = false
+++
### Dépendances
Installer les dépendances nécessaires : 

```
sudo pacman -S hyprland \
xdg-desktop-portal-hyprland \
dunst \
waybar \
wofi \
alacritty \
cliphist \
udiskie \
xwaylandvideobridge \
ttf-font-awesome
```

```
yay -S hyprpicker \
wl-clip-persist \
hyprland-per-window-layout
```

### Configuration Hyprland

Créer un dossier dans .config  nommé `hypr` quo contiendra les configs d'hyprland et d'hyprpaper : 
```
mkdir -p ~/.config/hypr
```

Placer ce fichier de config dans `~/.config/hypr/hyprland.conf` (Ajuster en conséquence les deux première lignes de monitor selon cette écriture) : 
`monitor=NomSortieVideo,highrr,0x0 ,1`

NomSortieVideo peut par exemple être DP-1 pour display port 1
highrr veut dire qu'on souhaite pour cette sortie la fréquence la plus élevé et la résolution la plus élevée supportées par l'affichage.
highres veut dire qu'on souhaite la résolution max supporté par l'affichage. 
0x0 correspond à la position de l'affichage, ici c'est donc l'origine. Pour mettre à droite un autre écran on doit donc indiquer 0 + la résolution horizontal de l'affichage à sa gauche soit pour un écran 2560x1440 ceci : 2560x0 exemple :  `monitor=DP-2,highrr,2560x0,1` et si on veut le mettre à gauche : `monitor=DP-2,highrr,-2560x0,1`

Le quatrième paramètre correspond au scaling (DPI).

Des solutions existe pour ajuster ça de façon graphique : https://github.com/luispabon/wdisplays et https://sr.ht/~emersion/kanshi/ pour avoir des profils.

```shell
##########################
# Monitors Configuration #
##########################

source = $HOME/.config/hypr/conf/monitors.conf

# Some default env vars.
env = XCURSOR_SIZE,24

# For all categories, see https://wiki.hyprland.org/Configuring/Variables/
input {
    kb_layout = fr,us
    kb_variant =
    kb_model =
    kb_options = grp:alt_shift_toggle
    kb_rules =
    mouse_refocus = false
    accel_profile = flat
    follow_mouse = 2
    float_switch_override_focus = 0

    touchpad {
        natural_scroll = false
    }

    sensitivity = 0 # -1.0 - 1.0, 0 means no modification.
}

general {
    # See https://wiki.hyprland.org/Configuring/Variables/ for more

    gaps_in = 3
    gaps_out = 10
    border_size = 2
    col.active_border = rgba(33ccffee) rgba(00ff99ee) 45deg
    col.inactive_border = rgba(595959aa)

    layout = dwindle

    # Please see https://wiki.hyprland.org/Configuring/Tearing/ before you turn this on
    allow_tearing = false
}

decoration {
    # See https://wiki.hyprland.org/Configuring/Variables/ for more

    rounding = 5

    blur {
        enabled = true
        size = 3
        passes = 1

        vibrancy = 0.1696
    }

    drop_shadow = true
    shadow_range = 4
    shadow_render_power = 3
    col.shadow = rgba(1a1a1aee)
}

animations {
    enabled = true

    # Some default animations, see https://wiki.hyprland.org/Configuring/Animations/ for more

    bezier = myBezier, 0.05, 0.9, 0.1, 1.05

    animation = windows, 1, 7, myBezier
    animation = windowsOut, 1, 7, default, popin 80%
    animation = border, 1, 10, default
    animation = borderangle, 1, 8, default
    animation = fade, 1, 7, default
    animation = workspaces, 1, 6, default
}

dwindle {
    # See https://wiki.hyprland.org/Configuring/Dwindle-Layout/ for more
    pseudotile = true # master switch for pseudotiling. Enabling is bound to mainMod + P in the keybinds section below
    preserve_split = true # you probably want this
}

master {
    # See https://wiki.hyprland.org/Configuring/Master-Layout/ for more
    new_is_master = true
}

gestures {
    # See https://wiki.hyprland.org/Configuring/Variables/ for more
    workspace_swipe = false
}

misc {
    # See https://wiki.hyprland.org/Configuring/Variables/ for more
    force_default_wallpaper = 0 # Set to 0 to disable the anime mascot wallpapers
}


# Example windowrule v1
# windowrule = float, ^(kitty)$
# Example windowrule v2
# windowrulev2 = float,class:^(kitty)$,title:^(kitty)$


# See https://wiki.hyprland.org/Configuring/Keywords/ for more
$mainMod = SUPER

source = ~/.config/hypr/conf/workspaces.conf

bind = $mainMod, Return, exec, alacritty
bind = $mainMod, C, killactive,
bind = $mainMod, M, exit,
bind = $mainMod, E, exec, thunar
bind = $mainMod, SPACE, togglefloating,
bind = $mainMod, R, exec, tofi-drun --drun-launch=true
bind = $mainMod, P, pseudo, # dwindle
bind = $mainMod, J, togglesplit, # dwindle
bind = $mainMod, L, exec, swaylock
bind = , XF86MonBrightnessUp, exec, xbacklight +10
bind = , XF86MonBrightnessDown, exec, xbacklight -10

#Power menu

bind = $mainMod, ESCAPE, exec, ~/.config/tofi/scripts/power-menu.sh "tofi --prompt-text=Power-menu:"
bind = , XF86PowerOff, exec, ~/.config/tofi/scripts/power-menu.sh "tofi --prompt-text=Power-menu:"


# Move focus with mainMod + arrow keys
bind = $mainMod, left, movefocus, l
bind = $mainMod, right, movefocus, r
bind = $mainMod, up, movefocus, u
bind = $mainMod, down, movefocus, d

bind = $mainMod, F, fullscreen

# Example special workspace (scratchpad)
bindm = $mainMod, mouse:272, movewindow
bindm = $mainMod, mouse:273, resizewindow

# Screenshot a window
bind = $mainMod, PRINT, exec, hyprshot -m window --clipboard-only
# Screenshot current monitor
bind = , PRINT, exec, hyprshot -m output --clipboard-only -c
# Screenshot a region
bind = $shiftMod, PRINT, exec, hyprshot -m region --clipboard-only

bind=, XF86AudioRaiseVolume, exec, wpctl set-volume -l 1.5 @DEFAULT_AUDIO_SINK@ 5%+
bindl=, XF86AudioLowerVolume, exec, wpctl set-volume @DEFAULT_AUDIO_SINK@ 5%-

general {
    allow_tearing = true
}

env = WLR_DRM_NO_ATOMIC,1

windowrulev2 = immediate, class:^(cs2)$

exec-once=dbus-update-activation-environment --systemd WAYLAND_DISPLAY XDG_CURRENT_DESKTOP

exec-once = /usr/lib/polkit-kde-authentication-agent-1
exec-once = waybar
exec-once = swww-daemon &
exec-once = udiskie &
exec-once = gammastep &
exec-once = wl-paste --type text --watch cliphist store #Stores only text data
exec-once = wl-paste --type image --watch cliphist store #Stores only image data
exec-once = nm-applet --indicator
exec-once = blueman-applet
exec-once = mako &
exec-once = xwaylandvideobridge &

bind = SUPER, V, exec, cliphist list | wofi --dmenu | cliphist decode | wl-copy

windowrulev2 = opacity 0.0 override 0.0 override,class:^(xwaylandvideobridge)$
windowrulev2 = noanim,class:^(xwaylandvideobridge)$
windowrulev2 = nofocus,class:^(xwaylandvideobridge)$
windowrulev2 = noinitialfocus,class:^(xwaylandvideobridge)$

# Mandatory for steam menus to work https://github.com/hyprwm/Hyprland/issues/2661#issuecomment-1821639125
windowrulev2 = stayfocused, title:^()$,class:^(steam)$
windowrulev2 = minsize 1 1, title:^()$,class:^(steam)$

windowrulev2 = workspace name:3:web,class:^(firefox)$

# Rule for PHPStorm breaking
windowrule = center, class:jetbrains-idea

env = QT_QPA_PLATFORMTHEME,qt6ct
env = XCURSOR_THEME,Breeze
env = SDL_VIDEODRIVER,wayland
env = CLUTTER_BACKEND,wayland
```

Les deux fichiers de configuration en dessous doivent être ajusté selon la disposition de vos écrans.

`~/.config/hypr/conf/workspaces.conf`
```shell
workspace = name:1:gaming,monitor:DP-1,default:true,border:false,shadow:false,decorate:false,gapsout:0,gapsin:0,persistent:true
workspace = name:2:comms,monitor:HDMI-A-1,default:true
workspace = name:3:web,monitor:DP-1,shadow:false,gapsout:0,gapsin:0
workspace = name:4:files,monitor:DP-1
workspace = name:5:terminal,monitor:DP-1

# Switch workspaces with mainMod + [0-9]
bind = $mainMod, ampersand, workspace,name:1:gaming
bind = $mainMod, eacute, workspace, name:2:comms
bind = $mainMod, quotedbl, workspace, name:3:web
bind = $mainMod, apostrophe, workspace, name:4:files
bind = $mainMod, parenleft, workspace, name:5:terminal
bind = $mainMod, minus, workspace, 6
bind = $mainMod, egrave, workspace, 7
bind = $mainMod, underscore, workspace, 8
bind = $mainMod, ccedilla, workspace, 9
bind = $mainMod, agrave, workspace, 10

# Move active window to a workspace with mainMod + SHIFT + [0-9]
bind = $mainMod SHIFT, ampersand, movetoworkspace,name:1:gaming
bind = $mainMod SHIFT, eacute, movetoworkspace, name:2:comms
bind = $mainMod SHIFT, quotedbl, movetoworkspace, name:3:web
bind = $mainMod SHIFT, apostrophe, movetoworkspace, name:4:files
bind = $mainMod SHIFT, parenleft, movetoworkspace, name:5:terminal
bind = $mainMod SHIFT, minus, movetoworkspace, 6
bind = $mainMod SHIFT, egrave, movetoworkspace, 7
bind = $mainMod SHIFT, underscore, movetoworkspace, 8
bind = $mainMod SHIFT, ccedilla, movetoworkspace, 9
bind = $mainMod SHIFT, agrave, movetoworkspace, 10
```

`~/.config/hypr/conf/monitors.conf`

```shell
# See https://wiki.hyprland.org/Configuring/Monitors/
monitor=DP-1,highrr,0x0,1
monitor=HDMI-A-1,2560x1440,-2560x0,1
```


### Configuration Waybar

Pour avoir une barre de statut en haut avec le CPU, l'heure etc... on va utiliser waybar : https://github.com/Alexays/Waybar

Placer ce fichier de configuration dans `~/.config/waybar/config` : 
```json
{
    // "layer": "top", // Waybar at top layer
    "position": "top", // Waybar position (top|bottom|left|right)
    "height": 30, // Waybar height (to be removed for auto height)
    // "width": 1280, // Waybar width
    "spacing": 4, // Gaps between modules (4px)
    // Choose the order of the modules
    "modules-left": ["hyprland/workspaces", "sway/mode", "sway/scratchpad", "custom/media"],
    "modules-center": ["hyprland/window"],
    "modules-right": ["idle_inhibitor", "pulseaudio", "network", "cpu", "memory", "temperature", "backlight", "keyboard-state", "sway/language", "battery", "battery#bat2", "clock", "tray"],
    // Modules configuration
    "hyprland/workspaces": {
        "format": "{icon}",
        "on-scroll-up": "hyprctl dispatch workspace e+1",
        "on-scroll-down": "hyprctl dispatch workspace e-1"
    },
    "keyboard-state": {
        "numlock": true,
        "capslock": true,
        "format": "{name} {icon}",
        "format-icons": {
            "locked": "",
            "unlocked": ""
        }
    },
    "sway/mode": {
        "format": "<span style=\"italic\">{}</span>"
    },
    "sway/scratchpad": {
        "format": "{icon} {count}",
        "show-empty": false,
        "format-icons": ["", ""],
        "tooltip": true,
        "tooltip-format": "{app}: {title}"
    },
    "mpd": {
        "format": "{stateIcon} {consumeIcon}{randomIcon}{repeatIcon}{singleIcon}{artist} - {album} - {title} ({elapsedTime:%M:%S}/{totalTime:%M:%S}) ⸨{songPosition}|{queueLength}⸩ {volume}% ",
        "format-disconnected": "Disconnected ",
        "format-stopped": "{consumeIcon}{randomIcon}{repeatIcon}{singleIcon}Stopped ",
        "unknown-tag": "N/A",
        "interval": 2,
        "consume-icons": {
            "on": " "
        },
        "random-icons": {
            "off": "<span color=\"#f53c3c\"></span> ",
            "on": " "
        },
        "repeat-icons": {
            "on": " "
        },
        "single-icons": {
            "on": "1 "
        },
        "state-icons": {
            "paused": "",
            "playing": ""
        },
        "tooltip-format": "MPD (connected)",
        "tooltip-format-disconnected": "MPD (disconnected)"
    },
    "idle_inhibitor": {
        "format": "{icon}",
        "format-icons": {
            "activated": "",
            "deactivated": ""
        }
    },
    "tray": {
        // "icon-size": 21,
        "spacing": 10
    },
    "clock": {
        // "timezone": "America/New_York",
        "tooltip-format": "<big>{:%Y %B}</big>\n<tt><small>{calendar}</small></tt>",
        "format-alt": "{:%Y-%m-%d}"
    },
    "cpu": {
        "format": "{usage}% ",
        "tooltip": false
    },
    "memory": {
        "format": "{}% "
    },
    "temperature": {
        // "thermal-zone": 2,
        // "hwmon-path": "/sys/class/hwmon/hwmon2/temp1_input",
        "critical-threshold": 80,
        // "format-critical": "{temperatureC}°C {icon}",
        "format": "{temperatureC}°C {icon}",
        "format-icons": ["", "", ""]
    },
    "backlight": {
        // "device": "acpi_video1",
        "format": "{percent}% {icon}",
        "format-icons": ["", "", "", "", "", "", "", "", ""]
    },
    "battery": {
        "states": {
            // "good": 95,
            "warning": 30,
            "critical": 15
        },
        "format": "{capacity}% {icon}",
        "format-charging": "{capacity}% ",
        "format-plugged": "{capacity}% ",
        "format-alt": "{time} {icon}",
        // "format-good": "", // An empty format will hide the module
        // "format-full": "",
        "format-icons": ["", "", "", "", ""]
    },
    "battery#bat2": {
        "bat": "BAT2"
    },
    "network": {
        // "interface": "wlp2*", // (Optional) To force the use of this interface
        "format-wifi": "{essid} ({signalStrength}%) ",
        "format-ethernet": "{ipaddr}/{cidr} ",
        "tooltip-format": "{ifname} via {gwaddr} ",
        "format-linked": "{ifname} (No IP) ",
        "format-disconnected": "Disconnected ⚠",
        "format-alt": "{ifname}: {ipaddr}/{cidr}"
    },
    "pulseaudio": {
        // "scroll-step": 1, // %, can be a float
        "format": "{volume}% {icon} {format_source}",
        "format-bluetooth": "{volume}% {icon} {format_source}",
        "format-bluetooth-muted": " {icon} {format_source}",
        "format-muted": " {format_source}",
        "format-source": "{volume}% ",
        "format-source-muted": "",
        "format-icons": {
            "headphone": "",
            "hands-free": "",
            "headset": "",
            "phone": "",
            "portable": "",
            "car": "",
            "default": ["", "", ""]
        },
        "on-click": "pavucontrol"
    },
    "custom/media": {
        "format": "{icon} {}",
        "return-type": "json",
        "max-length": 40,
        "format-icons": {
            "spotify": "",
            "default": "🎜"
        },
        "escape": true,
        "exec": "$HOME/.config/waybar/mediaplayer.py 2> /dev/null" // Script in resources folder
        // "exec": "$HOME/.config/waybar/mediaplayer.py --player spotify 2> /dev/null" // Filter player based on name
    }
}
```
Et ce fichier `~/.config/waybar/style.css` pour corriger les soucis de statut de workspace : 

```css
* {
    /* `otf-font-awesome` is required to be installed for icons */
    font-family: FontAwesome, Jetbrains Mono, Helvetica, Arial, sans-serif;
    font-size: 15px;
}

window#waybar {
    background-color: rgba(80, 89, 109, 0.5);
    color: #ffffff;
    transition-property: background-color;
    transition-duration: .5s;
}

window#waybar.hidden {
    opacity: 0.2;
}

/*
window#waybar.empty {
    background-color: transparent;
}
window#waybar.solo {
    background-color: #FFFFFF;
}
*/

window#waybar.termite {
    background-color: #3F3F3F;
}

window#waybar.chromium {
    background-color: #000000;
    border: none;
}

button {
    /* Use box-shadow instead of border so the text isn't offset */
    box-shadow: inset 0 -3px transparent;
    /* Avoid rounded borders under each button name */
    border: none;
    border-radius: 0;
}

/* https://github.com/Alexays/Waybar/wiki/FAQ#the-workspace-buttons-have-a-strange-hover-effect */
button:hover {
    background: inherit;
    box-shadow: inset 0 -3px #ffffff;
}

#workspaces button {
    padding: 0 10px;
    background-color: transparent;
    color: #ffffff;
    border-radius: 3px;

}

#workspaces button:hover {
    background: rgba(0, 0, 0, 0.2);
}

#workspaces button.active {
    background-color: #64727D;
}

#workspaces button.urgent {
    background-color: #eb4d4b;
}

#mode {
    background-color: #64727D;
    border-bottom: 3px solid #ffffff;
}

#clock,
#battery,
#cpu,
#memory,
#disk,
#temperature,
#backlight,
#network,
#pulseaudio,
#wireplumber,
#custom-media,
#tray,
#mode,
#idle_inhibitor,
#scratchpad,
#mpd {
    padding: 0 10px;
    color: #ffffff;
    border-radius: 3px;
    margin: 3px 0;
}

#window,
#workspaces {
    margin: 0 4px;
}

/* If workspaces is the leftmost module, omit left margin */
.modules-left > widget:first-child > #workspaces {
    margin-left: 0;
}

/* If workspaces is the rightmost module, omit right margin */
.modules-right > widget:last-child > #workspaces {
    margin-right: 0;
}

#clock {
    background-color: #1D1D2D;
}

#battery {
    background-color: #ffffff;
    color: #000000;
}

#battery.charging, #battery.plugged {
    color: #ffffff;
    background-color: #26A65B;
}

@keyframes blink {
    to {
        background-color: #ffffff;
        color: #000000;
    }
}

#battery.critical:not(.charging) {
    background-color: #f53c3c;
    color: #ffffff;
    animation-name: blink;
    animation-duration: 0.5s;
    animation-timing-function: linear;
    animation-iteration-count: infinite;
    animation-direction: alternate;
}

label:focus {
    background-color: #000000;
}

#cpu {
    background-color: #1D1D2D;
}

#memory {
    background-color: #1D1D2D;
}

#disk {
    background-color: #1D1D2D;
}

#backlight {
    background-color: #1D1D2D;
}

#network {
    background-color: #1D1D2D;
}

#network.disconnected {
    background-color: #1D1D2D;
}

#pulseaudio {
    background-color: #1D1D2D;
}

#pulseaudio.muted {
    background-color: #3d3d60;
}

#wireplumber {
    background-color: #fff0f5;
    color: #000000;
}

#wireplumber.muted {
    background-color: #f53c3c;
}

#custom-media {
    background-color: #1D1D2D;
    color: #2a5c45;
    min-width: 100px;
}

#custom-media.custom-spotify {
    background-color: #66cc99;
}

#custom-media.custom-vlc {
    background-color: #1D1D2D;
}

#temperature {
    background-color: #1D1D2D;
}

#temperature.critical {
    background-color: #eb4d4b;
}

#tray {
    background-color: #1D1D2D;
}

#tray > .passive {
    -gtk-icon-effect: dim;
}

#tray > .needs-attention {
    -gtk-icon-effect: highlight;
    background-color: #eb4d4b;
}

#idle_inhibitor {
    background-color: #1D1D2D;
}


#mpd {
    background-color: #1D1D2D;
}

#mpd.disconnected {
    background-color: #f53c3c;
}

#mpd.stopped {
    background-color: #90b1b1;
}

#mpd.paused {
    background-color: #51a37a;
}

#language {
    background: #1D1D2D;
    color: #ffffff;
    padding: 0 5px;
    margin: 0 5px;
    min-width: 16px;
}

#keyboard-state {
    background: #1D1D2D;
    color: #ffffff;
    padding: 0 0px;
    margin: 0 5px;
    min-width: 16px;
}

#keyboard-state > label {
    padding: 0 5px;
}

#keyboard-state > label.locked {
    background: rgba(0, 0, 0, 0.2);
}

#scratchpad {
    background: rgba(0, 0, 0, 0.2);
}

#scratchpad.empty {
        background-color: transparent;
}
```


### Raccourcis claviers

Super+Enter : Lancer le terminal alacritty
Super+R : Lancer une application avec Tofi
Super+F: Mettre une application en plein-écran
Super+Espace : Mettre l'application active en mode flottant
Maintien Super + Clic droit : Ajuster la taille de la fenêtre
Maintien Super + Clic gauche : Déplacer la fenêtre
Super+M : Quitter Hyprland
Super+[Touche de 1 à 9] : Changer de workspace
Super+Shift+[Touche de 1 à 9] : Déplacer la fenêtre dans un workspace
Alt+Shift : Changer de layout de clavier (FR et US)