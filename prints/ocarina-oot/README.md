# Ocarina of Time replica (MakerWorld 3282642) — X1C / 0.4 mm / Textured PEI only

Source: https://makerworld.com/en/models/3282642-ocarina-epic-zelda-ocarina-of-time-replica#profileId-3723617
Designer: Timberlake Creations. Profile 3723617 is the one you linked.

> Note: MakerWorld was unreachable from the session that wrote this, so the
> designer's exact wording could not be quoted. Everything below is written for
> the common layout of this kind of replica (two shell halves + separate
> mouthpiece/collar, printed on the textured plate, glued). Check the "Print
> Profile" tab and the designer's description once in Orca; if the parts
> differ, the orientation rule in step 2 still applies.

## 1. What "textured plate bottom" means

It is not part of the model. The designer sliced the profile for Bambu's
Textured PEI plate, so whatever face touches the bed comes out with the rough
PEI texture. Since you only own the textured plate, you cannot make that face
glossy. You can only choose **which face** gets it and **hide** it.

Fix without buying a plate: put the texture on the glue face. Ocarina replicas
are almost always split into two halves along the widest outline. Each half has
one flat inner face. Print each half with that flat inner face **down** on the
plate. The texture ends up inside the instrument where nobody sees it, and the
curved outside surface never touches the bed.

If the designer's profile already prints the halves this way, you are done and
the "textured bottom" only exists on the internal face. If instead a visible
face is on the plate (for example a flat-bottomed display base), you have two
options:

- Rotate so a hidden face is down (rotate 180° around X in Orca, re-add supports).
- Leave it and sand with 220 → 400 grit, then filler primer. Most people paint
  this model anyway (blue with gold/bronze collar), and primer erases the texture.

Do not use a raft to escape the texture; a raft bottom looks worse than PEI.
Do not lay tape or a glass sheet on the textured plate; it defeats the ABL
probe and gives a rough first layer anyway.

## 2. Orca Slicer setup (X1 Carbon, 0.4 mm, PLA)

Open the downloaded 3MF in Orca (File → Import → Import 3MF/STL, or drag it
onto the plate). Importing the 3MF brings in the designer's process, filament,
and printer settings. Then check each of these:

| Setting | Value | Why |
|---|---|---|
| Printer | Bambu Lab X1 Carbon 0.4 nozzle | matches your machine |
| Plate type (top bar) | **Textured PEI Plate** | must match the plate installed or the printer will pause / first layer fails |
| Process preset | 0.16 mm Optimal @BBL X1C (0.12 mm High Quality if you want the smoothest outside) | curved shell; 0.2 mm shows visible stair steps on the dome |
| Wall loops | 4 | strength for a hollow shell, hides infill |
| Top / bottom shell layers | 5 / 4 | solid outer skin |
| Sparse infill | 15 % gyroid | shell only; the sound chamber is already modeled hollow |
| Seam position | Aligned, then paint the seam onto the glue edge (Seam Painting tool) | keeps the seam invisible after gluing |
| Supports | Tree (organic), Auto, on build plate only where possible | overhangs on the mouthpiece and collar |
| Support top Z distance | 0.2 mm, 3 interface layers | clean removal |
| Slow down for overhangs | on (default) | curved dome quality |
| Elephant foot compensation | 0.15 mm | crisp glue edge on the first layer |

Filament (Bambu PLA Basic / Matte / Silk; matte hides layer lines best):

| Setting | Value |
|---|---|
| Nozzle | 220 °C (first layer 220 °C) |
| Bed (Textured PEI) | 65 °C first layer, 65 °C other layers |
| Cooling | default (100 % after layer 3) |
| Flow / pressure advance | run the on-printer flow calibration the first time you use this spool |

Community and Bambu defaults that are worth keeping on: Bed Leveling ON,
Flow Dynamics Calibration ON (first print with a spool), Timelapse OFF (it adds
a Z hop pause that leaves a mark on glossy PLA).

Slice and use the preview slider to check three things before sending:
1. The flat inner faces are on the plate (layer 1 shows two big outlines).
2. Supports touch only the mouthpiece/collar, not the dome.
3. The finger holes and windway have no support inside them (paint "support
   blocker" if they do).

## 3. Send to the X1C from Orca

Newer X1C firmware requires Bambu Connect for cloud printing from Orca.

1. Orca → Device tab → sign in to your Bambu account (or Prepare → printer
   dropdown → "Link printer" if it is not listed).
2. Slice plate, then click **Print plate** (dropdown next to "Slice plate").
3. If the printer is in Authorization Control / Bambu Connect mode, Orca opens
   Bambu Connect automatically with the sliced 3MF. If it does not, click
   **Export plate sliced file**, then open the .3mf in Bambu Connect.
4. In the send dialog: printer = your X1C, plate = **Textured PEI Plate**,
   map each filament to its AMS slot, Bed Leveling ON, Flow Calibration ON,
   Timelapse OFF. Send.
5. Watch the first layer on the camera. On textured PEI, PLA lines should look
   slightly squished with no gaps; if they are ropey, run a fresh bed level.

Post-print: remove supports while warm, sand the glue faces flat, CA glue or
epoxy the halves, then fill/prime/paint. The textured inner faces give the
glue extra bite.
