---
{"dg-publish":true,"dg-path":"Cheat sheets/Blender.md","permalink":"/cheat-sheets/blender/"}
---


# Notes

## General

- <kbd>F3</kbd>: command search
- <kbd>n</kbd>: show properties sidebar (includes view options)
- <kbd>Ctrl+Space</kbd>: maximize editor pane under pointer
- <kbd>m</kbd>: move to collection
- <kbd>F2</kbd>: rename selected object
- <kbd>Shift+a</kbd>: open Add menu
    - when adding a mesh, use the lowest resolution you can get away with (less vertices to deal with), and try to keep the faces roughly square
    - circles have a Fill Type, set to Ngon to add a face
- <kbd>Shift+d</kbd>: Duplicate
    - canceling while using tools with a movement step after (like Duplicate and Extrude) does not cancel the tool, only the movement, so there will still be duplicated vertices (undo to avoid this)
- <kbd>Alt+d</kbd>: Duplicate Linked
    - in Edit mode both the original and any linked duplicates will be affected, but in Object mode only the selected instance will
- <kbd>Tab</kbd>: switch between Object and Edit modes
- <kbd>Ctrl+Tab</kbd> (hold) - open mode select menu

## Viewport

- <kbd>Mouse3</kbd>: orbit (rotate) camera
- <kbd>Shift+Mouse3</kbd>: pan camera
- <kbd>~</kbd>: open view selection menu
- <kbd>Numpad 1</kbd>: front orthographic view
- <kbd>Numpad .</kbd>: focus on selected object
- <kbd>z</kbd> (hold) - open rendering mode menu

## Cursor

- the cursor controls where new objects are added, and affects how some tools work
- <kbd>Shift+Mouse2</kbd>: move cursor
- <kbd>Shift+s</kbd>: cursor movement menu
    - Cursor to Selected will move the cursor to the center of the selected object
- <kbd>Shift+c</kbd>: center scene and reset cursor

## Selection

- <kbd>a</kbd>: select all
    - <kbd>Alt+a</kbd>: deselect all
    - <kbd>Ctrl+i</kbd>: invert selection
- <kbd>w</kbd>: activate Select tool if another tool is active, cycle through Select modes if already active
    - <kbd>Shift+Mouse1</kbd>: add to selection
    - <kbd>Alt+Mouse1</kbd>: select loop
- when working in orthographic views, drag select instead of just clicking to select so that you get the vertices behind the front-most one
- <kbd>Alt+z</kbd>: toggle x-ray mode
    - allows you to select "through" a mesh
- <kbd>h</kbd>: hide selection
    - <kbd>Alt+h</kbd>: unhide
- <kbd>x</kbd>: delete selection (can also use <kbd>Delete</kbd>)
    - Dissolve will merge the surrounding geometry to fill in the space
- <kbd>Numpad /</kbd>: toggle local view (show only selection)
- <kbd>p</kbd>: se**p**arate selection
- <kbd>Ctrl+p</kbd>: parent objects
    - select the child first, then the parent-to-be, and select Keep Transform

## Camera

- <kbd>Numpad 0</kbd>: toggle camera view
- <kbd>Ctrl+Alt+Numpad 0</kbd>: move camera to current viewport position
- *Properties sidebar > View > Lock Camera to View* lets you move the camera by moving the view
    - or with the camera selected use <kbd>g</kbd> as usual, and use <kbd>Mouse3</kbd> to "zoom" (move along local z axis)
- <kbd>F11</kbd>: bring up most recent render
- <kbd>F12</kbd>: render

## Tools

- <kbd>g</kbd>: Move (g for grab)
- <kbd>r</kbd>: Rotate
- <kbd>s</kbd>: Scale
- <kbd>t</kbd>: Transform
- <kbd>Shift+Space</kbd>: open tools menu
- While a tool is in use:
    - <kbd>x/y/z</kbd>: restrict to that axis
    - <kbd>Shift+x/y/z</kbd>: exclude that axis
    - hold <kbd>Mouse3</kbd>: snap to nearby axis
    - hold <kbd>Ctrl</kbd>: snap to increments
    - hold <kbd>Shift</kbd>: slow down
    - can enter numeric values, and - for negatives

## Object mode

- *Right click > Shade Smooth/Flat*
- *Right click > Set Origin > Origin to Geometry* to move origin to center of each selected object

## Edit mode

- <kbd>1/2/3</kbd>: enter vertex/edge/face select mode
- <kbd>Ctrl+l</kbd>: select all connected (**l**inked) vertices
- <kbd>o</kbd>: toggle proportional editing
    - affects the area around the selection (scroll bar to adjust radius)
    - use menu in toolbar to change falloff type
- <kbd>Shift+Tab</kbd>: toggle snapping (use menu in toolbar to change what it snaps to)
    - Project Individual Elements allows all the vertices to snap when using proportional editing
- *Right click > Subdivide* (can adjust smoothness from popup)

### Edit mode tools

- <kbd>Ctrl+r</kbd>: Loop Cut (adds another "line" of vertices around the shape)
- <kbd>i</kbd>: Inset (like loop cuts but for the vertices of a single face)
- <kbd>e</kbd>: Extrude
    - <kbd>Alt+e</kbd>: open Extrude tools menu
    - <kbd>s</kbd> while extruding along normals - make extrusion even
    - extruding duplicates vertices - to move existing vertices, use the Move tool with only those vertices selected
- Spin: extrudes vertices in a circle around the 3D cursor
    - after spinning, can adjust center coordinates from popup menu
    - <kbd>Scroll wheel</kbd>: increase loop count
    - right click when moving to cancel and place in middle
- <kbd>Shift+e</kbd>: add crease to selected loop
- <kbd>f</kbd>: add face between selected edges
    - to do the same between edge loops use Bridge Edge Loops (<kbd>Ctrl+e</kbd>)

## Sculpting

- <kbd>f</kbd>: change brush size
    - *Right click on header > Tool Settings* to see brush settings
- <kbd>Shift+f</kbd>: change strength
- <kbd>Ctrl</kbd> (hold): push in instead of out

## Particles

- takes an object or collection and scatters it over the surface of another object (ex. sprinkles on a donut)
    - if using a collection, check Use Count to adjust the frequencies of different objects
    - checking Whole Collection will treat the collection as if it is one object
- use Emitter for animated particles, Hair for static
- to use an object as the particle, change *Render > Render As* to Object, and then select the object under Instance Object (same with a collection)
- *Render > Scale Randomness* will randomly scale, but along all three axes
- check the Advanced box to make rotation & other settings appear
    - under Rotation, Randomize adjusts rotation along all three axes, Randomize Phase adjusts rotation along relative X and Y (I think)
- check *Emission > Source > Use Modifier Stack* to improve results
- to limit the particles to the weight painted area, under Vertex Groups change Density to Group (the name of the vertex group created when you start weight painting)
    - disable particle rendering while weight painting to avoid slowdown

## Modifiers

- found under the wrench icon in Properties (bottom right)
- applied top to bottom, order matters
- can control visibility of modifiers in different modes
- use Apply in dropdown (next to delete button) to apply the changes to the mesh (good idea to make a copy first)

### Available Modifiers:

- Subdivision Surface: non-destructively smooths the surface by subdividing faces
- Solidify: adds thickness (extrudes)
    - offset controls direction
    - use crease (under Edge Data) to adjust generated shape
- Array: creates arrays of the same object (use multiple Array modifiers for multi-dimensional arrays)

## Materials

- Roughness defines how reflective the material is
- Subsurface allows light to enter and bounce around within the object, like skin or icing (need to set the subsurface color)
    - Subsurface Radius defines how deeply the light penetrates the object (separate values for R/G/B)
    - for the donut icing, set the subsurface color to a bit deeper shade than the base color, and set the Subsurface Radius for the channel matching the icing color to 0.3 and the others to 0.15
- Emission makes a material "glow"

## Shading

<kbd>Ctrl+Shift+Mouse1</kbd> on a node: "preview" by connecting to Material Output/Surface

- to randomize base color of a material: *Object Info/Random > ColorRamp/Fac (customize gradient as desired), ColorRamp/Color > Principled BDSF/Base Color*
    - change Gradient Type to Constant (from Linear) to avoid interpolation
- to apply bump mapping: *Texture Coordinate/Object > Noise Texture/Vector, Noise Texture/Fac > Displacement/Height, Displacement/Displacement > Material Output/Displacement*
    - To actually affect the mesh, in Material properties change *Settings > Surface > Displacement* to Displacement & Bump (change Scale in Displacement node if it looks like a porcupine)
- use a MixRGB node to combine multiple textures (Fac slider controls the "strength" of the second input)
    - use Blend type Add with Fac 1.00 to "merge" B&W textures

## Texture Paint

- <kbd>n</kbd>: bring up brush options & color picker
    - can change blend types too
- <kbd>x</kbd>: swap colors
- <kbd>s</kbd>: sample color
- <kbd>Alt+s</kbd> with cursor over texture: save image
- purple is the "missing texture" color
- use a Texture Mask in the brush options to paint with a texture - set the mask type in Texture properties on the right
    - in brush options set Mask Mapping to Random to avoid perfectly tiling the texture
- use Shading mode to hook up the texture (as an Image Texture node) to the Base Color (or any other property) of a material

## Rendering

- hiding objects from viewport does not disable them in rendering
- use the Cycles rendering engine for path tracing (but much slower and noisier preview than Eevee)
- sun lights do not have any fall off
- light angle affects shadow sharpness (0 = sharp, higher = smoother)
- can adjust exposure in Render Properties tab under Color Management
- change *Color Management > View Transform* to False Color to check brightness - everything should be gray or green (see https://blender.stackexchange.com/a/195862)

## Compositing

- denoising through compositor seems to give better results than using checkbox in Properties

# Links

[Blender Beginner Tutorial - Part 1](https://www.youtube.com/watch?v=TPrnSACiTJ4&list=PLjEaoINr3zgEq0u2MzVgAaHEBt--xLB6U)

[Become a 3D illustrator in one hour!](https://polygonrunway.com/p/become-a-3d-illustrator-in-one-hour)
