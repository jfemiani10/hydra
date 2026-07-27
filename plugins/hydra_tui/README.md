# hydra-tui

A terminal UI for exploring and launching [Hydra](https://hydra.cc) configs.

```bash
pip install -e plugins/hydra_tui
python my_app.py --tui
```

The `--tui` flag is provided by the companion patch in `hydra/_internal/utils.py`
on this fork. All UI code lives here, so hydra-core never depends on Textual.

## What it does

- Lists every config group and option, read from the running app's own
  `ConfigLoader` — so it adapts to any Hydra project with no configuration.
- Recomposes the config live as you arrow through options, using
  `load_configuration()`, the same call `@hydra.main` makes.
- Shows the exact `python my_app.py ...` command your selection maps to, so the
  UI teaches the CLI instead of replacing it.
- Composition errors (bad types, unknown keys) render in the pane instead of
  crashing the UI.
- `r` launches the job in a subprocess and streams output; `m` toggles
  `--multirun`.

## Keys

| Key | Action |
| --- | --- |
| arrows | change the selected option in a group |
| `r` | run the composed job |
| `m` | toggle `--multirun` |
| `x` | clear the log pane |
| `q` | quit |
