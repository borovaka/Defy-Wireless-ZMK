# Defy Wireless ZMK

Your own keymap for the Dygma Defy wireless running ZMK. This repository holds only your keymap and settings; the firmware comes from the [zmk-defy-wireless](https://github.com/borovaka/zmk-defy-wireless) module, pinned by its release tag. Read that module's README first: it explains what the port supports, its risks, and how the keyboard is installed and taken back to stock.

This is a community project, not affiliated with or endorsed by Dygma.

## Quick start

1. Click **Use this template** on GitHub and create your repository from it. Keep it public, so that the installer can download its builds.
2. Edit [config/defy_neuron.keymap](config/defy_neuron.keymap), on GitHub in the browser if you like, and commit. GitHub Actions builds the firmware in a few minutes.
3. Get [defy-installer](https://github.com/borovaka/defy-installer) as the module's [quick start](https://github.com/borovaka/zmk-defy-wireless#quick-start) shows, with one command.
4. With the Neuron plugged into the computer, run, from the installer's folder:

   ```sh
   ./defy-installer update --from yourname/your-repo
   ```

   On Windows the program is `.\defy-installer.exe`. On a keyboard that still runs Dygma's firmware, use `install` in place of `update`; it asks for the halves' cables when it needs them.
5. After each later change, commit, wait for the build and run `./defy-installer update`. A keymap change can also go onto the Neuron by [drag and drop](#by-drag-and-drop).

The sections below explain each step.

## Make it yours

1. Click **Use this template** on GitHub and create your repository from it.
2. Edit [config/defy_neuron.keymap](config/defy_neuron.keymap) for the keymap, [config/defy_neuron.conf](config/defy_neuron.conf) for the Neuron's settings, and [config/defy.conf](config/defy.conf) for the halves'. The Neuron holds the keymap, with the lighting, and talks to the computers; the halves only report keys. The two `.conf` files list the settings you are most likely to change, each with the value the firmware uses now. All are commented out except mouse keys, which `defy_neuron.conf` turns on.
3. Push. GitHub Actions builds the firmware. On the default branch it also replaces the release tagged `firmware` with the new images, which is where the installer finds them.

The keymap here is Dygma's default layout, as Bazecor sets it up, on the Base, Navigation and FKeys layers. The Defy controls are on keys that layout leaves empty, and a fourth layer, Mouse, holds mouse keys. The module's README lists the [layers and controls](https://github.com/borovaka/zmk-defy-wireless#keymap). Comments marked DEMO in the keymap show ZMK behaviours to try and change: the copy key's tap-dance, the hold-tap timing of the Ctrl and Alt thumb keys, mouse keys, and a free thumb key to bind in ZMK Studio. [ZMK's documentation](https://zmk.dev/docs/keymaps) covers the keymap syntax, behaviours, combos and macros.

The lighting is set in the keymap too. The right upper outer thumb key turns it on and off, and the key right of G steps through the looks. Hold FKeys, the left upper thumb key second from the outside, and press ; or ' for dimmer or brighter, or / for the next look. The Neuron remembers your choice. Out of the box the keys on Base light their white LEDs. Navigation, FKeys and Mouse light only the keys they use, a colour for each group, over green, purple and blue underglow. To change that, edit the three blocks near the top of the keymap:

- `&defy_lighting`: the looks, each a colour for the keys and one for the underglow.
- `&defy_layer_lighting`: a layer's own colours, such as red keys on a gaming layer.
- `&defy_key_lighting`: single keys in a colour of their own, such as WASD.

The module's [lighting](https://github.com/borovaka/zmk-defy-wireless/blob/main/docs/lighting.md#normal-lighting) page lists the colours. A build with new looks starts from them, whatever the keys chose before.

You can also change the keymap on the keyboard itself with [ZMK Studio](https://zmk.studio), without building anything; hold FKeys and press O once to unlock it. Studio's changes last until `defy-installer install` or `update` next writes the Neuron and clears them, so that the keymap from this repository takes effect. A drag-and-drop copy keeps them; see below. Keep the layout you want to last here.

## Installing your keymap

[defy-installer](https://github.com/borovaka/defy-installer) installs it. The first time, name your repository:

```sh
defy-installer update --from yourname/your-repo
```

The installer saves that in its settings file. After each push, once the build has finished, run:

```sh
defy-installer update
```

It downloads the `firmware` release, checks it against its `manifest.json`, and writes what changed. After a keymap change that is only the Neuron, with no cables to move. `install` does the same on a keyboard that still runs Dygma's firmware. To go back to the module's release, with the keymap this template starts from, run `defy-installer update --from default`.

The installer can download releases only from a public repository. For a private one, download the `firmware` artifact from the run under **Actions** and run `defy-installer update --firmware firmware.zip`.

| File | Goes to |
|---|---|
| `left-hybrid-radio.bin`, `right-hybrid-radio.bin` | The halves. Each carries the radio firmware it installs when its radio runs another version |
| `neuron-hybrid.hex` | The Neuron. It holds your keymap, so after a keymap change only this one is new |
| `neuron-hybrid.uf2` | The Neuron's ZMK alone, for drag and drop; see below |
| `manifest.json` | What the build holds: the module version, this repository and commit, and each image's size and SHA-256 |

The artifact also holds `.uf2` copies of the halves' images and the radio's own `radio.bin`, `radio.seal` and `radio.c`, from which the halves' images are built; the installer does not use them.

### By drag and drop

After a keymap change you can also copy the Neuron's image yourself, as on other ZMK keyboards:

1. Plug the Neuron into your computer with its USB cable.
2. Hold FKeys, the left upper thumb key second from the outside, and press Backspace, the right half's top outer key. A drive called `DEFY` appears.
3. Copy `neuron-hybrid.uf2` onto it.

The Neuron writes it, restarts into ZMK and the drive goes away. Your computer may say the drive was not ejected properly; that is harmless. Bluetooth pairings and settings stay. To leave the drive without copying anything, unplug the Neuron.

Changes saved in ZMK Studio stay too, and where they differ from the keymap you copied, they win. `defy-installer update` clears them when it writes the Neuron; if the Neuron already runs the build you copied, answer yes when it asks whether to write it again anyway. Studio's "Restore Stock Settings" clears them too.

This covers the Neuron only. Use the installer for a first install, to go back to Dygma's firmware, and after you change the module version, which changes the halves too.

## Building on your computer

With Docker installed:

```sh
./build.sh                  # both halves and the Neuron
./build.sh neuron-hybrid    # only the Neuron, after a keymap change
```

The images land in `firmware/`, with `manifest.json`. The first run fetches ZMK, Zephyr and the module into this folder, about 3 GB; git ignores them. The module's README lists the [other targets](https://github.com/borovaka/zmk-defy-wireless#build-targets). To install a local build, run `defy-installer update --firmware firmware`.

## Updating the firmware

The module's version is the tag in [config/west.yml](config/west.yml) and in [.github/workflows/build.yml](.github/workflows/build.yml). Change both to a newer release tag and push. The module's release notes say what changed and whether the halves need new images too. For builds on your computer, also copy the container image's digest from the module's `scripts/zmk` at that tag into [build.sh](build.sh).

## Licence

MIT; see [LICENSE](LICENSE). Add your own copyright line there for your changes.
