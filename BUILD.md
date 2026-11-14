# Building engine ports

Use Linux ARM64 with Python 3, GCC/G++, binutils and the standard development tools. Work on a writable checkout. Output files go below the selected build root and each port's ignored build directory. The OS runtime builder image can supply this environment; see the OS repository docs/BUILD.md.

```sh
export RHYTHMER_BUILD_ROOT="$PWD/build/workspace"
mkdir -p "$RHYTHMER_BUILD_ROOT/move-probes"
gcc -O2 -std=gnu11 tools/probe_move.c -ldl -o "$RHYTHMER_BUILD_ROOT/move-probes/probe"
python3 tools/compile_fm1.py cs1 elixir fm106 fmini guru600 hortator kwasub kwasub-kit mono2k obervave prophet s1vave
python3 tools/compile_drumx.py
python3 tools/compile_ports.py sophie
```

The FM-1 originals and Sophie DSP are included in ports/. DRUM-X performs its original-reference test before linking. External Prophet and Elixir banks are deliberately not regenerated; zero-count external tables preserve Init/Initkit. Compile outputs must be staged with their generated plugin metadata and licenses; a shared object alone is not a complete installation.

## Original Move modules

The thin Move wrappers dynamically load a native module; compiling a wrapper does not compile the original DSP. Full pinned sources and dependencies are under third_party/move/{aphex,braids,dexed,hera,plaits}. Each supplies scripts/build.sh and its original build rules (some invoke Docker). Build each original module using those rules, retain its module metadata and resources, and place the output module directory at RHYTHMER_BUILD_ROOT/<module_folder> from the corresponding ports/move-*/SOURCE.json. The original release URL, SHA256, DSP hash and commit are recorded there for reproducible reference downloads.

```sh
python3 tools/compile_moves.py aphex braids dexed hera plaits
```

The wrappers stage into build/workspace/move-stage/Plugins. Preserve the original bank and license files when staging the modules. A source rebuild may differ from the published binary hash; do not edit acceptance locks to represent an untested build as tested. Regenerate a catalogue and run the real Rhythmer host tests before distributing modified engines.

## Host integration

The authored Prophet/Elixir presets, kit layouts, chromatic playback bridge and pad-specific UI are Rhythmer host features. Their integration and complete tests are in the OS repository. These source-build commands do not create a signed official update; installation/recovery is documented in the OS repository.
