### `choco install fluidsynth` v2.4.3 works but the default v2.4.4 fails.
```
v2.4.3: 'libfluidsynth-3' was found as C:\tools\fluidsynth\bin\libfluidsynth-3.dll.
v2.4.4: 'libfluidsynth-3' was found as C:\tools\fluidsynth\bin\libfluidsynth-3.dll
    But ctypes.CDLL(lib) fails https://github.com/FluidSynth/fluidsynth/issues/1510
v2.4.5: 'fluidsynth-3' was found as C:\tools\fluidsynth\bin\fluidsynth-3.dll.
```

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
        fluidsynth-version: [ 2.4.3, 2.4.4, 2.4.5 ]
    runs-on: windows-latest
    steps:  # fluidsynth v2.4.3 works but the default v2.4.4 fails.
      - run: choco install fluidsynth --version ${{ matrix.fluidsynth-version }}
      - run: fluidsynth --version
```
