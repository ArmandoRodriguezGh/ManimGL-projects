# ManimGL Environment Context

Give this file to an LLM before asking for ManimGL code or debugging help.

## Project identity

This project uses **3Blue1Brown's ManimGL**, not Manim Community Edition.

Use:

```python
from manimlib import *
```

Render with:

```bash
manimgl scene.py SceneClass
```

Do not silently replace these with ManimCE syntax:

```python
from manim import *
```

```bash
manim -pql scene.py SceneClass
```

ManimGL and ManimCE are related but incompatible projects. Their APIs, imports, commands, configuration systems, and examples must not be mixed.

## Known machine configuration

- macOS
- Apple Silicon / ARM64
- MacBook Pro with an M3 processor
- zsh shell
- Homebrew package manager
- Local scripts edited in a normal IDE or text editor and run from Terminal

## Known installation

Repository:

```text
https://github.com/3b1b/manim.git
```

Known clone path:

```text
/Users/verxta/manim
```

The repository was installed in editable mode with a command equivalent to:

```bash
cd /Users/verxta/manim
python3 -m pip install -e .
```

Editable mode means the installed package points to the local repository. A `git pull`, branch change, commit checkout, or local source edit can change ManimGL without another `pip install`.

Previously observed:

```text
ManimGL v1.7.2
Python 3.13
```

Previously observed executable area:

```text
/Library/Frameworks/Python.framework/Versions/3.13/bin/
```

Verify these values rather than assuming they are still current.

## Environment status

The existing installation appears to have been made in a global Python environment, not necessarily a dedicated virtual environment.

Before changing packages, inspect:

```bash
which python3
python3 --version
python3 -m pip --version
which manimgl
manimgl --version
python3 -c "import manimlib; print(manimlib.__file__)"
```

On this Mac, `python` may not exist outside a virtual environment. Use `python3` unless an activated environment provides `python`.

If a virtual environment is active, use:

```bash
which python
python --version
python -m pip --version
which manimgl
```

## Known system dependencies

These were installed during Manim setup:

```bash
brew install ffmpeg
brew install pkg-config cairo
brew install --cask mactex
```

Known components:

- FFmpeg
- Cairo
- pkg-config
- MacTeX / LaTeX
- dvisvgm, normally supplied through the TeX installation

Verify with:

```bash
ffmpeg -version
pkg-config --version
pkg-config --modversion cairo
latex --version
dvisvgm --version
```

These system packages are not fully recorded by `pip freeze`.

## Python dependency history

A previous launch failed with:

```text
ModuleNotFoundError: No module named 'trimesh'
```

The installation later progressed to scene execution, so `trimesh` was likely installed or the issue was otherwise resolved. Verify rather than assuming:

```bash
python3 -m pip show trimesh
```

Inside a virtual environment:

```bash
python -m pip show trimesh
```

## Exact dependency snapshot

When returning to this environment later, create a package snapshot with:

```bash
python3 -m pip freeze > requirements-manimgl-lock.txt
```

Inside an activated virtual environment:

```bash
python -m pip freeze > requirements-manimgl-lock.txt
```

This records exact Python package versions. It does not record Homebrew packages or the current Git commit.

Also record the ManimGL source revision:

```bash
git -C /Users/verxta/manim remote -v
git -C /Users/verxta/manim branch --show-current
git -C /Users/verxta/manim rev-parse HEAD
git -C /Users/verxta/manim status
```

## Current ManimGL coding conventions

Basic example:

```python
from manimlib import *


class ExampleScene(Scene):
    def construct(self):
        circle = Circle()
        self.play(ShowCreation(circle), run_time=2)
        self.wait(1)
```

Run it with:

```bash
manimgl scene.py ExampleScene
```

A full path also works:

```bash
manimgl "/absolute/path/to/scene.py" ExampleScene
```

`cd` only accepts directories. Do not try:

```bash
cd scene.py
```

Instead:

```bash
cd "/path/to/folder"
manimgl scene.py ExampleScene
```

## Compatibility issues already encountered

### Correct import

Use:

```python
from manimlib import *
```

Do not use:

```python
from manim import *
```

### `OldTex` is unavailable

This older code failed:

```python
OldTex(r"\pi")
```

Use:

```python
Tex(r"\pi")
```

unless the current repository source indicates otherwise.

### Obsolete `self.play` syntax

This older syntax failed:

```python
self.play(grid.shift, LEFT)
```

with an error saying the bound method could not be converted to an animation.

Use:

```python
self.play(grid.animate.shift(LEFT))
```

### macOS persistence message

This message is not normally the real failure:

```text
ApplePersistenceIgnoreState: Existing state will not be touched.
```

Read the Python traceback that follows it.

## Known working-style scene

```python
from manimlib import *
import numpy as np
import math


class AnimatingMethods(Scene):
    def construct(self):
        grid = Tex(r"\pi").get_grid(10, 10, height=4)
        self.add(grid)

        self.play(grid.animate.shift(LEFT))
        self.play(grid.animate.set_color(YELLOW))
        self.wait()

        self.play(
            grid.animate.set_submobject_colors_by_gradient(BLUE, GREEN)
        )
        self.wait()

        self.play(grid.animate.set_height(TAU - MED_SMALL_BUFF))
        self.wait()

        self.play(
            grid.animate.apply_complex_function(np.exp),
            run_time=5,
        )
        self.wait()

        self.play(
            grid.animate.apply_function(
                lambda p: [
                    p[0] + 0.5 * math.sin(p[1]),
                    p[1] + 0.5 * math.sin(p[0]),
                    p[2],
                ]
            ),
            run_time=5,
        )
        self.wait()
```

Run:

```bash
manimgl my_scene.py AnimatingMethods
```

## 3Blue1Brown source repositories

ManimGL engine:

```text
https://github.com/3b1b/manim
```

Public video scene code:

```text
https://github.com/3b1b/videos
```

Old video scenes may depend on:

- Historical Manim versions
- `manim_imports_ext`
- Custom helper classes
- Local assets
- Fonts
- Data files
- Project-specific utilities

Do not assume old scenes run unchanged under the current ManimGL revision.

## Current animation concept

A planned scene uses a 3D Gaussian-peak landscape:

```python
def landscape(u, v, alpha):
    peaks = [
        (-2.0, -1.0, 1.7, 0.7),
        (0.0, 0.5, 2.2, 0.8),  # selected peak
        (1.8, -0.8, 1.5, 0.6),
        (2.2, 1.5, 1.8, 0.9),
        (-1.5, 1.8, 1.4, 0.7),
    ]

    z = 0.0

    for index, (x0, y0, amplitude, sigma) in enumerate(peaks):
        scale = 1.0 if index == 1 else alpha
        z += (
            amplitude
            * scale
            * np.exp(
                -((u - x0) ** 2 + (v - y0) ** 2)
                / (2 * sigma**2)
            )
        )

    return np.array([u, v, z])
```

The intended visual may contain:

- A person or symbolic marker on the selected peak
- An orbiting or tracking camera
- Competing peaks that decay
- A selected peak that remains fixed or rises
- A gradient-descent, optimization, attention, winner-take-all, or focus metaphor

Implement this with ManimGL-compatible OpenGL, surface, camera, and updater APIs. Do not transplant ManimCE code without checking compatibility.

## Debugging instructions for the LLM

When diagnosing an error:

1. Read the complete traceback.
2. Find the first traceback frame inside the user's scene file.
3. Confirm the command is `manimgl`.
4. Confirm the import is `from manimlib import *`.
5. Check which Python installation owns `manimgl`.
6. Separate dependency errors from scene API, OpenGL, LaTeX, and path errors.
7. Inspect the installed source path.
8. Check the current Git commit when behavior differs from examples.
9. Do not recommend global upgrades until the active environment is identified.
10. Do not mix ManimCE documentation or CLI flags into ManimGL guidance.

Useful checks:

```bash
which python3
which manimgl
python3 --version
manimgl --version
python3 -c "import manimlib; print(manimlib.__file__)"
head -n 1 "$(which manimgl)"
```

## Updating behavior

The environment does not normally update automatically.

Python packages change when commands such as these are run:

```bash
python3 -m pip install <package>
python3 -m pip install --upgrade <package>
python3 -m pip install -r requirements.txt
```

Editable ManimGL source changes when the local repository changes:

```bash
git -C /Users/verxta/manim pull
git -C /Users/verxta/manim checkout <branch-or-commit>
```

System packages change when Homebrew upgrades them:

```bash
brew upgrade
```

A requirements lock file is only a snapshot. It does not automatically enforce or update the environment.

## Values that must be verified

Do not invent:

- Exact active Python version
- Exact `manimgl` path
- Exact ManimGL Git commit
- Exact branch
- Exact Python package versions
- Whether a virtual environment is active
- Exact FFmpeg, Cairo, LaTeX, and `trimesh` versions
- Exact OpenGL behavior
