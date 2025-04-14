### `choco install fluidsynth` v2.4.3 works but v2.4.4 fails.
```
v2.4.3: 'libfluidsynth-3' was found as C:\tools\fluidsynth\bin\libfluidsynth-3.dll.
v2.4.4: 'libfluidsynth-3' was found as C:\tools\fluidsynth\bin\libfluidsynth-3.dll
    But ctypes.CDLL(lib) fails https://github.com/FluidSynth/fluidsynth/issues/1510
v2.4.5: 'fluidsynth-3' was found as C:\tools\fluidsynth\bin\fluidsynth-3.dll.
    Windows MSVS build DLL name change `libfluidsynth-3` --> `fluidsynth-3`.
```

* v2.4.4: https://github.com/FluidSynth/fluidsynth/issues/1541
* v2.4.5: https://github.com/FluidSynth/fluidsynth/issues/1543
* Both:   https://github.com/FluidSynth/fluidsynth/issues/1544

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
      matrix:  # https://community.chocolatey.org/packages/fluidsynth
        fluidsynth-version: [ 2.4.3, 2.4.4, 2.4.5 ]
    runs-on: windows-latest
    steps:  # fluidsynth v2.4.3 works but v2.4.4 fails and v2.4.5 on X64 works under a renamed DLL.
      - name: "choco install fluidsynth --version=${{ matrix.fluidsynth-version }} on ${{ runner.os }} on ${{ runner.arch }}"
        run: choco install fluidsynth --version=${{ matrix.fluidsynth-version }}
      - run: fluidsynth --version  # Fails on Windows fluidsynth v2.4.4 and v2.4.5
        continue-on-error: true
      # v2.4.3: 'libfluidsynth-3' was found as C:\tools\fluidsynth\bin\libfluidsynth-3.dll.
      # v2.4.4: 'libfluidsynth-3' was found as C:\tools\fluidsynth\bin\libfluidsynth-3.dll
      #     But ctypes.CDLL(lib) fails https://github.com/FluidSynth/fluidsynth/issues/1510
      # v2.4.5: 'fluidsynth-3' was found as C:\tools\fluidsynth\bin\fluidsynth-3.dll.
      #     Library is renamed on Windows MSVC builds on X64.
      - shell: python
        run: |
          import ctypes
          import ctypes.util
          import os
          
          def find_libfluidsynth(debug_print: bool = False) -> str:
              libs = "fluidsynth fluidsynth-3 libfluidsynth libfluidsynth-3 libfluidsynth-2 libfluidsynth-1"
              for lib_name in libs.split():
                  if lib := ctypes.util.find_library(lib_name):
                      if debug_print:
                          print(f"'{lib_name}' was found as {lib}.")
                      return lib
              raise FileNotFoundError
  
          # os.add_dll_directory() is only available on Windows and does nothing useful!
          # if hasattr(os, "add_dll_directory"):  # Only available on Windows
          #     os.add_dll_directory("C:\\tools\\fluidsynth\\bin")
          os.environ["PATH"] += ";C:\\tools\\fluidsynth\\bin"
          lib = find_libfluidsynth(debug_print=True)
          print(f"{ctypes.CDLL(lib) = }")
```
