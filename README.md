### `choco install fluidsynth` v2.4.3 works but the default v2.4.4 fails.
* https://github.com/FluidSynth/fluidsynth/issues/1541
```yaml
name: choco
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  workflow_dispatch:
jobs:
  ci:
    strategy:
      fail-fast: false
      matrix:
        fluidsynth-version: [ 2.4.3, 2.4.4, , 2.4.5 ]
    runs-on: windows-latest
    steps:  # fluidsynth v2.4.3 works but the default v2.4.4 fails.
      - run: choco install fluidsynth --version ${{ matrix.fluidsynth-version }}
      - run: fluidsynth --version
```
