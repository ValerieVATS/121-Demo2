# D2: Sticker Sketchpad by Valeri and James

[Sketchpad](https://valerievats.github.io/121-Demo2/)

Its a 2D drawing tool for strokes and stickers. Drawings are stored as replayable commands rather than as pixels.

## Using the sketchpad

Use a desktop browser with a mouse or trackpad.

### Draw and place stickers

- Select Normal, Thin, or Thick, then press and drag on the canvas
  to draw. Release to finish the stroke.
- Select a sticker, press on the canvas, drag to position it, and release
  to place it.
- The selected tool is highlighted.
- Press Escape during a gesture to cancel it without adding content.
- Pointer capture allows a gesture to finish when you release outside
  the canvas. Pointer cancellation discards the unfinished gesture.
- Changing tools does not change previously placed content.

### Create custom stickers

1. Enter a name in Sticker name.
2. Enter an emoji or short text in Emoji or text.
3. Click Create Sticker.
4. Select the new tool from the palette and place it like any preset sticker.

You can create multiple separately selectable tools in one session.
Both fields must contain non-whitespace text. Empty submissions do not
create a tool or change drawing history. To abandon an unsubmitted tool,
clear its fields or leave it unsubmitted.

Custom tool definitions and drawings are not saved across page reloads.

### Undo, redo, and redraw

- Undo removes the most recent completed stroke or sticker placement.
- Redo restores the most recently undone action.
- Each completed gesture counts as one action.
- Unavailable history controls are disabled.
- Committing a new mark after undoing discards the abandoned redo branch.
- Creating or selecting tools and cancelling gestures do not discard redo.
- Redraw briefly clears the drawing surface, then reconstructs the
  picture from the retained commands without deleting the document.

Clear permanently removes all drawing content and resets both undo and
redo history. Clear cannot be undone. Tool definitions remain available.

### Export a PNG

Click Export PNG to download `drawing.png`.

The interactive canvas has an 800 × 600 backing resolution. Export creates
a separate 1600 × 1200 canvas and replays the active drawing commands at
twice the scale.

The PNG:

- Includes completed content that has not been undone.
- Preserves drawing order, positions, sizes, and styles.
- Includes preset and custom stickers.
- Excludes unfinished gestures.
- Has an explicitly painted white background.

Export does not reset the drawing, selected tool, or history. You can
continue drawing and using Undo and Redo afterward.

## Run from source

### Prereq

- [Git](https://git-scm.com/downloads)
- [Deno 2.9.7](https://docs.deno.com/runtime/getting_started/installation/)
- A modern browser supporting Canvas 2D and Pointer Events
- This project uses TypeScript and Vite 8.3.1 through Deno.

### Initial setup

```sh
git clone https://github.com/ValerieVATS/121-Demo2.git
cd 121-Demo2
deno task setup
deno task dev
```

`deno task setup` installs the local Git pre-commit checks. Run it once
for each clone.

The first development run downloads the required tooling and dependencies.
Open the local URL printed by Vite, normally `http://localhost:3000/`.

### Check and build

```sh
deno task ci
```

To apply the project's formatting:

```sh
deno task fmt
```

### Preview the production build

```sh
deno task preview
```

Open the URL printed by Vite.

### Internet and offline use

Initial setup requires internet access to obtain Git/Deno, clone the repository, and download Vite and its dependencies.

Once the required dependencies are cached, local development and builds can run without internet access. The drawing application itself does not fetch remote images, fonts, or API data.

## Dev guide

### Example extension: add a rectangle tool

A rectangle would be a new tool.

We would need to change: [`src/main.ts`](src/main.ts):

1. Define and register the tool.
   Extend `Tool` to represent a rectangle tool and its appearance settings. Add a rectangle definition to `tools`. Because `showTools` generates buttons from that collection, it can display the new definition without a rectangle-specific palette implementation.

2. Record rectangle geometry
   We add rectangle data containing its position, dimensions, fill&stroke settings, and stroke width. Save those appearance values when creating the gesture rather than consulting the selected tool during later replay.

3. Handle the rectangle gesture.
   Then extend the `pointerdown`, `pointermove`, and `pointerup` handlers. Record the starting corner on press, update the opposite corner during dragging, and commit one rectangle command on release by user. Also a separate unfinished rectangle state. Extend `endGesture` so cancellation clears it along with other transient state.

4. Render the rectangle.
   Finally we add a rectangle drawing operation accepting its recorded data and the supplied target context. Use do.Extend `render` to show the unfinished rectangle as a preview. Make a `DrawCommand` whose `draw(target)` operation renders the completed rectangle data.

5. Reuse history and reuse export changed to take rectangle tool.

No rectangle change should be needed in `src/style.css`, `index.html`, or `vite.config.js`.

## Credits and references

- [MDN Canvas tutorial](https://developer.mozilla.org/en-US/docs/Web/API/Canvas_API/Tutorial)
- [MDN Pointer Events](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events)
- [Vite deployment documentation](https://vite.dev/guide/static-deploy.html)

The D2 scaffold was authored with OpenCode/OpenAI GPT-6.1 Sol assistance. Planning and README checking used OpenCode/OpenAI GPT-6.1 Sol assistance. Dependencies retain their respective licenses.
