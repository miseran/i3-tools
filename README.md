# i3-tools
A few scripts I use with the i3 window manager. Actually, there's only one right
now, but more might follow.

## focus-tool

focus-tool has two modes:

`focus-tool [left|right|up|down]` focuses the container that is *visually* in
the given direction of the current window. This differs from the default
behaviour of i3, which selects the most recently focused container in whatever
structure lies in the given direction.

As an example, suppose your windows are laid out in a 2x2 grid as follows:

    ┌────────────────┐┌────────────────┐
    │                ││                │
    │                ││                │
    │       A        ││       C        │
    │                ││                │
    │                ││                │
    ├────────────────┤├────────────────┤
    │                ││                │
    │                ││                │
    │       B        ││       D        │
    │                ││                │
    │                ││                │
    └────────────────┘└────────────────┘

That is, the workspace is split horizontally, and each of its two children is
split vertically. Suppose you first focus D, and then somehow switch to A.
If you now execute the built-in command `focus right`, i3 would end up focusing
window D, since it was most recently active in the right split.
If you run `focus-tool right`, however, you will end up focusing C, which is
what most people would expect.

Something similar applies if the right container is split horizontally. The
built-in command might appear to jump over one window to the most recently
focused one, whereas focus-tool does the intuitive thing.

If there are multiple windows that can be considered to lie in the specified
direction, focus-tool will *usually* choose the most recently focused one
among them. For example, given the following layout:

    ┌────────────────┐┌────────────────┐
    │                ││                │
    │                ││       C        │
    │       A        ││                │
    │                │├────────────────┤
    │                ││                │
    │                ││       D        │
    ├────────────────┤│                │
    │                │├────────────────┤
    │       B        ││                │
    │                ││       E        │
    │                ││                │
    └────────────────┘└────────────────┘

with A currently focused, the command `focus-tool right` will move to the most
recently focused window among C and D, but never E.
(Note: In some more contrived layouts, this does not always work. I do not
consider this important enough to warrant a fix.)

Some more remarks:
 - The script supports moving between multiple monitors, as long as they are
   in a reasonable layout.
 - `focus-tool [direction]` doesn't wrap. This is intentional.
 - The tool also doesn't change the currently focused tab in a tabbed or stacked
   container. Instead, it uses the above logic considering only currently
   visible tabs. Use the second mode to change tabs.


`focus-tool [tab-prev|tab-next]` mode cycles through the children of the
innermost tabbed or stacked container which contains the currently focused
window. It can also cycle through floating windows. 


Suggested key bindings:

    bindsym $mod+h exec --no-startup-id "path/to/focus-tool left"
    bindsym $mod+j exec --no-startup-id "path/to/focus-tool down"
    bindsym $mod+k exec --no-startup-id "path/to/focus-tool up"
    bindsym $mod+l exec --no-startup-id "path/to/focus-tool right"

    bindsym $mod+bracketleft exec --no-startup-id "path/to/focus-tool tab-prev"
    bindsym $mod+bracketright exec --no-startup-id "path/to/focus-tool tab-next"


focus-tool depends on [i3ipc-python](https://github.com/acrisci/i3ipc-python) by
acrisci.
