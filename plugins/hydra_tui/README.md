# hydra-tui

A terminal UI for exploring and launching [Hydra](https://hydra.cc) configs.

The `--tui` flag is provided by the companion patch in `hydra/_internal/utils.py`
on this fork. All UI code lives here, so hydra-core never depends on Textual.

## Setup from a fresh clone

`--tui` only exists on this fork, so it must be installed from source. Building
hydra from source **requires Java** — the config-override grammar is generated
with ANTLR at build time (the jar itself is vendored in `build_helpers/bin/`).
Check with `java -version`; install a JDK first if that fails.

**Windows:** enable long paths first, or the clone fails partway through with
`Filename too long`. Hydra has test fixtures with paths over the 260-character
`MAX_PATH` limit:

```bash
git config --global core.longpaths true
```

(Cloning into a short directory such as `C:\src` also avoids it. If a clone
already failed this way, fix the setting and run `git restore --source=HEAD :/`
rather than re-cloning.)

```bash
git clone -b tui https://github.com/jfemiani10/hydra.git
cd hydra

python -m venv .venv
# Windows: .venv\Scripts\activate       Linux/macOS: source .venv/bin/activate

pip install -r requirements/dev.txt
pip install -e .                     # hydra core (runs ANTLR, needs Java)
pip install -e plugins/hydra_tui     # the TUI plugin
```

Verify the flag is present:

```bash
python -c "from hydra._internal.utils import get_args_parser; \
print('--tui' in [o for a in get_args_parser()._actions for o in a.option_strings])"
# -> True
```

## Try it

A self-contained example ships with the plugin:

```bash
python plugins/hydra_tui/example/my_app.py --tui
```

It also works on any of hydra's own examples, with no changes:

```bash
python examples/tutorials/basic/your_first_hydra_app/6_composition/my_app.py --tui
```

On Windows, run it from **Windows Terminal** — Textual renders poorly in the
legacy console host.

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
