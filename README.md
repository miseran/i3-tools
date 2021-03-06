# i3-tools
A few scripts I use with the i3 window manager.
These scripts require Python 3 and depend on
[i3ipc-python](https://github.com/acrisci/i3ipc-python) by acrisci.



## window-tool

window-tool is used to focus and move containers in a more natural way.

`window-tool focus [left|right|up|down]` focuses the container that is
*visually* in the given direction of the current window. This differs from the
default behaviour of i3, which selects the most recently focused container in
whatever structure lies in the given direction.

As an example, suppose your windows are laid out in a 2x2 grid as follows:

    ┌────────────┐┌────────────┐
    │            ││            │
    │     A      ││     C      │
    │            ││            │
    ├────────────┤├────────────┤
    │            ││            │
    │     B      ││     D      │
    │            ││            │
    └────────────┘└────────────┘

That is, the workspace is split horizontally, and each of its two children is
split vertically. Suppose you first focus D, and then directly switch to A.
If you now execute the built-in command `focus right`, i3 would end up focusing
window D, since it was most recently active in the right split.
If you run `window-tool focus right`, however, you will end up focusing C, which
in my opinion is more intuitive.

Something similar applies if the right container is split horizontally. The
built-in command might appear to jump over one window to the most recently
focused one, whereas window-tool does the intuitive thing.

If there are multiple windows that can be considered to lie in the specified
direction, window-tool will *usually* choose the most recently focused one
among them. This can fail in some more contrived layouts.

 - The script supports moving between multiple monitors, as long as they are
   in a reasonable layout.
 - `window-tool focus [direction]` doesn't wrap. This is intentional.
 - The tool also doesn't change the currently focused tab in a tabbed or stacked
   container. Instead, it uses the above logic considering only currently
   visible tabs. Use `tab-focus` to change tabs.


`window-tool move [left|right|up|down] AMOUNT` moves the current container in
the given direction (by AMOUNT pixels if floating). This differs from the
default behaviour of i3 in that if the current window is contained in a
tabbed or stacked container, it immediately escapes that container rather than
moving within it. Use `tab-move` to move tabs within a container.


`window-tool swap [left|right|up|down]` swaps the current container with the one
in the given direction, using the same logic as above.


`window-tool tab-focus [prev|next]` cycles through the children of the
innermost tabbed or stacked container which contains the currently focused
window. It can also cycle through floating windows. 


`window-tool tab-move [prev|next]` moves the innermost tab, and works even if
the tabs are further subdivided. Does not wrap.


Example configuration:

    bindsym $mod+h exec --no-startup-id "path/to/window-tool focus left"
    bindsym $mod+j exec --no-startup-id "path/to/window-tool focus down"
    bindsym $mod+k exec --no-startup-id "path/to/window-tool focus up"
    bindsym $mod+l exec --no-startup-id "path/to/window-tool focus right"

    bindsym $mod+Shift+h exec --no-startup-id "path/to/window-tool move left"
    bindsym $mod+Shift+j exec --no-startup-id "path/to/window-tool move down"
    bindsym $mod+Shift+k exec --no-startup-id "path/to/window-tool move up"
    bindsym $mod+Shift+l exec --no-startup-id "path/to/window-tool move right"

    bindsym $mod+bracketleft  exec --no-startup-id "path/to/window-tool tab-focus prev"
    bindsym $mod+bracketright exec --no-startup-id "path/to/window-tool tab-focus next"

    bindsym $mod+Shift+bracketleft  exec --no-startup-id "path/to/window-tool tab-move prev"
    bindsym $mod+Shift+bracketright exec --no-startup-id "path/to/window-tool tab-move next"



## resize-tool

resize-tool resizes the current container. It will adjust the outer gaps instead
if there is only one container.

`resize-tool resize [horizontal|vertical|left|right|top|bottom] AMOUNT` resizes the
current container by AMOUNT (which can be negative). If the current container is
in a split layout, this works like the regular `resize`. But if it is the only
container, it does the resizing by adjusting the outer gaps. (Here, a single
top-level tabbed or stacked container counts as a single container, even if it
has multiple children.)

`resize-tool resize [natural|orthogonal] AMOUNT` works the same, but the direction is
chosen according to the current layout. If you are in a vertically split
container, `natural` resizes vertically, otherwise horizontally. `orthogonal`
does the opposite.

Use the `smart_gaps inverse_outer` setting to enable these outer gaps only when
there is just one window.


Example configuration:

    gaps inner      20
    gaps outer      0
    gaps horizontal 240
    smart_gaps inverse_outer

    bindsym  $mod+minus       exec --no-startup-id "path/to/resize-tool resize natural    -80"
    bindsym  $mod+equal       exec --no-startup-id "path/to/resize-tool resize natural    +80"
    bindsym  $mod+Shift+minus exec --no-startup-id "path/to/resize-tool resize orthogonal -80"
    bindsym  $mod+Shift+equal exec --no-startup-id "path/to/resize-tool resize orthogonal +80"



## workspace-tool

workspace-tool is used to change workspaces in a multi-monitor setup. Unlike
the regular way i3 works, this tool treats workspaces as independent from their
output.

`workspace-tool fetch WORKSPACE` shows the given workspace on the currently
active output. If that workspace was already visible on another output, swap
them. You can undo this command by repeating it.

`workspace-tool rename WORKSPACE` renames the current workspace to the given
name. If that workspace already exists, it swaps names. You can undo this
command by repeating it.

`workspace-tool previous` switches to the previous workspace, just like
repeating the last `fetch` command.

`workspace-tool swap` swaps the workspaces on your outputs, if you have exactly
two monitors. (If you have more, it will swap with an arbitrary other output,
which is not very useful.)


Example configuration:

    bindsym $mod+m       focus output right
    bindsym $mod+Shift+m move container to output right
    bindsym $mod+Ctrl+m  exec --no-startup-id "path/to/workspace-tool swap"

    bindsym $mod+1       exec --no-startup-id "path/to/workspace-tool fetch 1"
    bindsym $mod+Shift+1 move container to workspace 1
    bindsym $mod+Ctrl+1  exec --no-startup-id "path/to/workspace-tool rename 1"
    # etc.
