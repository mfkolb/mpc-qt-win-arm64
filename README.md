# MPC-QT for Windows on ARM

Unofficial port of [MPC-QT](https://github.com/mpc-qt/mpc-qt) to Windows on ARM devices.

MPC-QT is a media player in the spirit of Media Player Classic, built on Qt and [mpv](https://mpv.io/). The official project publishes Windows builds for x64 only. They run on ARM laptops (such as Snapdragon X machines) through emulation. This fork builds MPC-QT natively for ARM64, so the player and its video pipeline run without emulation.

> **This is not an official MPC-QT release.** Please report problems with these builds here, not to the MPC-QT developers. All credit for the player itself goes to the [MPC-QT contributors](https://github.com/mpc-qt/mpc-qt/graphs/contributors).

## Download

Builds are produced automatically by GitHub Actions. Open the latest successful [Msys2 CI (ARM64)](https://github.com/mfkolb/mpc-qt-win-arm64/actions/workflows/windows-arm64.yml) run and download one of the files listed under **Artifacts** (a GitHub account is required):

- **mpc-qt-win-arm64-installer**: installs to `C:\Program Files\MPC-QT` and adds a Start menu shortcut and file associations.
- **mpc-qt-win-arm64-continuous**: portable zip. Extract it and run `mpc-qt.exe`.

The builds are not code-signed, so Windows SmartScreen will warn when you run them. Choose **More info → Run anyway**.

To check that it's running natively, open Task Manager → **Details**, add the **Architecture** column, and look for **ARM64** next to `mpc-qt.exe`.

### Switching from the official x64 version

The ARM64 installer uses the same install folder and registry entries as the official x64 installer. Installing one over the other replaces it in place, so you won't end up with two copies. Close MPC-QT before installing. Your settings are kept, because they're stored in your user profile rather than the install folder.

## What's different from upstream

The player's source code is unchanged. The differences are all in how it's built:

- **A separate ARM64 workflow** ([`windows-arm64.yml`](.github/workflows/windows-arm64.yml)) builds on GitHub's Windows ARM runners using MSYS2's `clangarm64` toolchain.
- **mpv is rebuilt with OpenGL enabled.** MSYS2's ARM64 mpv package is built with `-Dgl=disabled`. MPC-QT draws video through mpv's OpenGL render API, so with that package the player exits on launch with `Could not create render context`. The workflow rebuilds mpv from MSYS2's own recipe without that option.
- **The release and installer scripts are not tied to x64.** [`make-release-msys2.sh`](scripts/make-release-msys2.sh) and the NSIS installer take their architecture from the build instead of hardcoding x64. The official x64 build is unaffected.

## Known limitations

- **Unsigned:** see the SmartScreen note above.
- **Development builds:** this fork follows MPC-QT's development branch, not its stable releases.
- **Some helpers are still x64:** `yt-dlp.exe` (used for YouTube and other web links) and QuickJS are the x64 builds and run under emulation. They only do short bursts of work while a stream loads.
- **The installer itself is x64:** MSYS2 has no ARM64 build of NSIS, so the installer program runs under emulation. Everything it installs is native ARM64.
- **mpv is rebuilt on every build:** this adds only a couple of minutes, but it depends on the layout of MSYS2's mpv recipe. The aim is to get the OpenGL option re-enabled upstream in MSYS2, which would remove the step entirely.

## Building it yourself

You can fork this repository and run the **Msys2 CI (ARM64)** workflow from the Actions tab, so no local setup is needed. For local builds, see the [upstream build instructions](https://github.com/mpc-qt/mpc-qt#compiling-on-windows) and the steps in [`windows-arm64.yml`](.github/workflows/windows-arm64.yml), using the `clangarm64` MSYS2 environment.

## License

MPC-QT is licensed under the [GNU General Public License v2](LICENSE), and so is this fork. The source for every build is this repository.
