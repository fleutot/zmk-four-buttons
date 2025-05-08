Example keyboard shield config for ZMK, with a minimal "proof of concept" split keyboard of only four keys per half.

# Build

```
podman volume create --driver local -o o=bind -o type=none  -o device=</path/to/this/>zmk-four-buttons/ zmk-config
```

Using your clone of `zmk`:
```
podman build -t zmk-config -f Dockerfile ~/src/keyboard/zmk/.devcontainer/
```

Start a prompt in the podman container:
```
podman run -it --rm   --security-opt label=disable   --workdir /workspaces/zmk   -v $HOME/src/keyboard/zmk:/workspaces/zmk   -v $HOME/src/keyboard/zmk-four-buttons:/workspaces/zmk-config -p 3000:3000   zmk-config /bin/bash
```

In this prompt, if not already initialized:

```
west init -l app
west update
```

Then:
```
cd app
west build -d build/left -b nice_nano_v2 -- -DSHIELD=four_buttons_left \
  -DZMK_CONFIG="/workspaces/zmk-config"
```
(note that this last path does *not* end with `/config`, although the [doc](https://zmk.dev/docs/development/local-toolchain/build-flash?build-opts=addonMcu#building-from-zmk-config-folder) says it should. It does not work with this ending.

The resulting uf2 file is in the `zmk` build tree, `zmk/app/build/left/zephyr/zmk.uf2`

Then do the same with the right half.
