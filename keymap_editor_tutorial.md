# Keymap Editor Tutorial

> A practical guide to using [nickcoutsos.github.io/keymap-editor](https://nickcoutsos.github.io/keymap-editor/) for ZMK keyboards — covering key remapping, combos, behaviors, macros, and more.

---

## Table of Contents

1. [Getting Started — Connect Your Repo](#1-getting-started--connect-your-repo)
2. [The Interface at a Glance](#2-the-interface-at-a-glance)
3. [Remapping Keys](#3-remapping-keys)
4. [Adding and Editing Combos](#4-adding-and-editing-combos)
5. [Creating Custom Behaviors](#5-creating-custom-behaviors)
   - [Hold-Tap (Homerow Mods)](#hold-tap-homerow-mods)
   - [Tap-Dance](#tap-dance)
   - [Sticky Key (One-Shot)](#sticky-key-one-shot)
6. [Creating Macros](#6-creating-macros)
7. [Conditional Layers](#7-conditional-layers)
8. [Saving and Building](#8-saving-and-building)
9. [Tips and Gotchas](#9-tips-and-gotchas)

---

## 1. Getting Started — Connect Your Repo

### Option A: GitHub Integration (recommended)

This is the easiest workflow — changes are committed directly to your repo and trigger a firmware build automatically.

1. Go to [nickcoutsos.github.io/keymap-editor](https://nickcoutsos.github.io/keymap-editor/)
2. Click **GitHub** as your keymap source
3. Click **Sign in with GitHub** and authorize the app
   - The app only requests access to your repositories — it does not store your keymap data externally
4. Select your `zmk-config` repository from the list
5. Select the `.keymap` file (e.g. `config/lily58.keymap`)
6. Your keyboard layout will render visually on screen

### Option B: Clipboard (no GitHub account needed)

1. Open your `.keymap` file locally
2. Copy the entire contents (`Ctrl+A`, `Ctrl+C`)
3. In the editor, choose **Clipboard** as source
4. Paste your keymap
5. When done, click **Keymap** → copy the generated code back into your file

### Option C: File System (Chromium only)

1. Choose **File System** as source
2. Select your `.keymap` file directly from your computer
3. Changes are saved back to disk automatically

> **Tip:** Use a Chromium-based browser (Chrome, Edge, Brave) for the best experience. Firefox does not support the File System API.

---

## 2. The Interface at a Glance

```
┌─────────────────────────────────────────────┐
│  [Keymap Editor]   Source ▾   Settings ⚙    │
├──────────┬──────────────────────────────────┤
│          │                                  │
│  Layers  │         Keyboard Layout          │
│  ──────  │   (click any key to remap it)    │
│  Base    │                                  │
│  Lower   │                                  │
│  Raise   │                                  │
│          │                                  │
├──────────┴──────────────────────────────────┤
│  [Combos]  [Behaviors]  [Macros]            │
└─────────────────────────────────────────────┘
```

- **Left sidebar** — your layers. Click to switch between them.
- **Main area** — visual layout of your keyboard. Click any key to edit its binding.
- **Bottom tabs** — Combos, Behaviors, Macros editors.
- **Top right** — Settings, dark mode toggle, source switcher.

---

## 3. Remapping Keys

1. Click any key on the visual layout
2. A binding editor popup appears
3. Start typing a key name — the search is fuzzy:
   - Type `lctl` → finds `LCTRL`
   - Type `play` → finds `C_PLAY_PAUSE`
   - Type `mo 1` → finds the `&mo` behavior with layer 1
4. Select the binding from the dropdown
5. Click outside to close — the key is updated immediately

### Useful binding shortcuts

| What you want | Type in search |
|---------------|----------------|
| Regular key | `A`, `SPACE`, `ESC` |
| Hold a layer | `mo` → select layer number |
| Toggle a layer | `tog` |
| One-shot layer | `sl` |
| Modifier | `lshift`, `lctl`, `lgui` |
| Modifier + key | `ls` → `LS(A)` |
| Transparent (pass-through) | `trans` |
| Blocked key | `none` |
| Media | `c_play`, `c_vol_up` |
| Bluetooth profile | `bt` → `BT_SEL 0` |
| Reset | `sys_reset` |
| Bootloader | `bootloader` |

---

## 4. Adding and Editing Combos

Combos let you trigger a key or behavior by pressing two or more keys simultaneously.

### Adding a new combo

1. Click the **Combos** tab at the bottom of the editor
2. Click **+ Add Combo**
3. A new combo entry appears with:
   - **Key positions selector** — a mini layout of your keyboard
   - **Binding** — what the combo outputs
   - **Timeout** — how many milliseconds you have to press both keys (default: 50ms)
   - **Layers** — optionally restrict the combo to specific layers
   - **Slow release** — toggle for advanced use cases

4. Click the keys on the mini layout to select which physical keys trigger the combo
   - Selected keys highlight in blue
   - You can select 2 or more keys

5. Set the **binding** — same search as remapping a regular key
   - You can bind combos to custom behaviors and macros too!

6. Adjust **timeout** if needed:
   - `30–50ms` → fast typist, less accidental triggers
   - `80–100ms` → more forgiving, easier to activate

### Example: Add `=` on `R`+`T` (like your current config)

1. Add Combo
2. Click `R` and `T` on the mini layout
3. Binding → search `equal` → select `EQUAL`
4. Timeout → `50`
5. Done

### Editing existing combos

All combos from your `.keymap` file are automatically imported and shown in the Combos tab. Click any field to edit it.

---

## 5. Creating Custom Behaviors

Behaviors are the power feature of ZMK — they let a single key do different things depending on how you press it.

Go to the **Behaviors** tab at the bottom.

### Hold-Tap (Homerow Mods)

Hold-Tap lets a key act as a modifier when held and a regular key when tapped. This is the basis of "homerow mods" — putting Ctrl/Shift/Alt/GUI on your home row keys.

1. Click **+ Add Behavior**
2. Choose `zmk,behavior-hold-tap` as the type
3. Give it a label, e.g. `hm` (homerow mod)
4. Configure the properties:

| Property | Recommended value | What it does |
|----------|-------------------|--------------|
| `tapping-term-ms` | `200` | Time in ms to decide tap vs hold |
| `flavor` | `tap-preferred` | Resolves ambiguity in favor of tap |
| `bindings` | `&kp`, `&kp` | First = hold action, second = tap action |

5. Click **Save**
6. Now go to a key on your layout → search for `hm` → you'll see your new behavior
7. Select it and set the two parameters:
   - First: the modifier (e.g. `LSHIFT`)
   - Second: the key (e.g. `A`)

**Result:** Tap `A` → types `a`. Hold `A` → acts as `Left Shift`.

#### Flavor options explained

| Flavor | Best for |
|--------|----------|
| `tap-preferred` | Homerow mods — prioritizes tapping |
| `hold-preferred` | Layer keys — prioritizes holding |
| `balanced` | Middle ground |
| `tap-unless-alone` | Only holds if another key is pressed simultaneously |

### Tap-Dance

Tap-Dance triggers different actions based on how many times you tap a key.

1. Add Behavior → type `zmk,behavior-tap-dance`
2. Label: e.g. `td_shift`
3. Set `tapping-term-ms`: `200`
4. Add bindings (one per tap count):
   - First binding: single tap action
   - Second binding: double tap action
5. Bind the behavior to any key

**Example:** Single tap = `Shift`, Double tap = `Caps Lock`

### Sticky Key (One-Shot)

A sticky key activates a modifier for exactly one subsequent keypress — useful for one-shot Shift.

1. Add Behavior → type `zmk,behavior-sticky-key`
2. Label: e.g. `sk_shift`
3. Set `release-after-ms`: `1000` (how long it stays active)
4. Binding: `&kp`
5. Bind to a key → set parameter to `LSHIFT`

**Result:** Tap → next key is shifted. Tap twice → stays active until you tap again.

---

## 6. Creating Macros

Macros play a sequence of key events when triggered — useful for common phrases, shortcuts, or complex key combinations.

### Simple macro (e.g. type your email)

1. Go to **Macros** tab → click **+ Add Macro**
2. Give it a name, e.g. `email`
3. In the bindings list, click **+ Add Binding**
4. Add a sequence:
   - `&macro_tap &kp H` → taps H
   - `&macro_tap &kp E` → taps E
   - ... and so on
5. Or use `&kp` with modifier combos for shortcuts:
   - `&macro_press &kp LCTL` → hold Ctrl
   - `&macro_tap &kp C` → tap C (= Copy)
   - `&macro_release &kp LCTL` → release Ctrl
6. Save → bind to any key by searching for your macro name

### Macro control bindings explained

| Binding | What it does |
|---------|--------------|
| `&macro_tap` | Tap the next key |
| `&macro_press` | Hold the next key down |
| `&macro_release` | Release the next key |
| `&macro_pause_for_release` | Wait until the macro key is released |
| `&macro_wait_time 100` | Wait 100ms before continuing |

### Macro example: Ctrl+Shift+T (reopen tab)

```
&macro_press   &kp LCTL
&macro_press   &kp LSHIFT
&macro_tap     &kp T
&macro_release &kp LSHIFT
&macro_release &kp LCTL
```

---

## 7. Conditional Layers

A conditional layer activates automatically when two other layers are both active — useful for a "function layer" that appears when Lower + Raise are both held.

1. Go to **Settings** (top right) → look for **Conditional Layers** or find it in the layer list
2. Click **+ Add Conditional Layer**
3. Set:
   - **If layers**: select two layers (e.g. Lower + Raise)
   - **Then layer**: the layer that activates (e.g. Adjust)
4. Create the Adjust layer with whatever keys you want available when both modifiers are held

---

## 8. Saving and Building

### With GitHub integration

Every change is committed to your repo automatically when you make it. After editing:

1. Changes are auto-committed to `main` (you'll see commit messages like "Update keymap bindings")
2. GitHub Actions triggers automatically
3. Go to your repo → **Actions** tab → wait for the build to complete
4. Download `firmware.zip` from the artifacts
5. Flash as usual

### With Clipboard / File System

1. Click **Keymap** button (top of the editor)
2. Copy the generated code
3. Paste it into your `lily58.keymap` file
4. Commit and push to GitHub manually
5. Wait for GitHub Actions to build

---

## 9. Tips and Gotchas

**Search is your best friend**
Every behavior, keycode, and modifier is searchable. You never need to remember exact names — just type roughly what you want.

**You can always edit the keymap file directly**
The editor plays nicely with hand-edited keymaps. If you add a feature the editor doesn't support yet, just write it in the file — the editor will leave it alone when you open the file again.

**Key positions, not key labels**
Combos use physical key positions (numbered left-to-right, top-to-bottom), not key labels. The mini layout in the combo editor handles this for you visually, but be aware if you ever edit combos in the `.keymap` file directly.

**Tapping term tuning**
If your homerow mods misfire while typing fast, lower `tapping-term-ms` (try `150`). If they don't activate when you want them to, raise it (try `250`).

**The editor won't break unsupported features**
If you're using a ZMK feature the editor doesn't know about (like pointing/mouse keys), it will leave those bindings intact — you just can't edit them through the UI.

**`&trans` vs `&none`**
- `&trans` — the key "falls through" to the layer below
- `&none` — the key does nothing and blocks lower layers

**Dark mode**
Click the moon/sun icon in the top right. Your eyes will thank you.

---

## Further Reading

- [ZMK Behaviors Documentation](https://zmk.dev/docs/behaviors/key-press)
- [ZMK Combos Documentation](https://zmk.dev/docs/features/combos)
- [Keymap Editor Wiki](https://github.com/nickcoutsos/keymap-editor/wiki)
- [Hold-Tap Use Cases](https://zmk.dev/docs/behaviors/hold-tap#use-cases)
EOF