# jarrunner/teavm

A fork of [konsoletyper/teavm](https://github.com/konsoletyper/teavm), kept for [jarrunner/jr](https://github.com/jarrunner/jr), a Java launcher built with TeaVM's C backend and compiled with llvm-mingw (Windows), clang (macOS) and zig (Linux).

The fork exists so that jr builds from source fixes rather than by editing the generated C after the fact. Each fix is a small separate commit, offered upstream; nothing here is meant to diverge from TeaVM.

## Branches

One branch per upstream release, cut from its tag: `0.16.0-jr` is TeaVM 0.16.0 plus:

- `build: make the publication groupId configurable`, so this fork can publish under its own groupId (the default stays `org.teavm`).
- `c: detect Windows by target when compiling with mingw`: mingw compilers define `__GNUC__`, so a Windows build was classified as UNIX.
- `c: use the uchar.h fallback for mingw's msvcrt target`: mingw declares `mbrtoc16`/`c16rtomb` only for the UCRT.
- `c: give Address.add() a void * result`: a `char *` result does not convert to a typed pointer parameter of an `@Import` function.

## Artifacts

Published as `io.github.jarrunner.teavm:<artifact>:<upstream version>-jr.<n>`, with the same artifact names as TeaVM (`teavm-core`, `teavm-classlib`, `teavm-tooling`, ...). To build them into the local Maven repository:

```
gradlew :core:publishToMavenLocal :classlib:publishToMavenLocal :tools:core:publishToMavenLocal ... -Pteavm.project.version=0.16.0-jr.1 -Pteavm.publish.groupId=io.github.jarrunner.teavm
```

(jr needs `classlib`, `core`, `extension:apis`, `extension:spi`, `extension:spi-util`, `interop:core`, `jso:apis`, `jso:core`, `jso:impl`, `metaprogramming:api`, `metaprogramming:impl`, `platform`, the seven `relocated:libs:*` projects, and `tools:core`.)

## New upstream release

Cut `<version>-jr` from the new tag, cherry-pick whichever of the commits above upstream has not taken, and publish `<version>-jr.1`.

## Releases on Maven Central

- `0.16.0-jr.1` (2026-10-09): the 20 modules jr needs (`teavm-core`, `teavm-classlib`, `teavm-tooling`, `teavm-interop`, `teavm-platform`, `teavm-jso*`, `teavm-metaprogramming-*`, `teavm-extension-*`, `teavm-relocated-libs-*`), built from this branch at the four commits above.

TeaVM's own JReleaser path (`release.sh`) did not sign on Windows with Git's gpg, so this release was staged with `publishAllPublicationsToTeavmRepository`, signed with gpg (`--pinentry-mode loopback`), and uploaded as a bundle to the Central Portal API. A repeatable release (GitHub Actions) is still to do.
