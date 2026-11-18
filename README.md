# lio-sam-wsl

**LIO-SAM Studio for Windows, delivered through WSL2.**

A Windows installer and Start-menu app that runs the proven Ubuntu 24.04 + ROS 2 Jazzy build of
[LIO-SAM-V2](https://github.com/band72/LIO-SAM-V2) inside a dedicated WSL2 distribution. WSLg shows the
app windows and RViz on the Windows desktop, so users never see Linux.

> **Status: planning / scaffolding.** Nothing is built yet. See [TODO.md](TODO.md).

This is **Route A** of the [Windows packaging analysis](https://github.com/band72/LIO-SAM-V2/blob/main/Enabling%20Windows%20Capable%20Solution.md):
the fastest path to Windows users (estimated ~2–3 weeks), with low–medium risk.

## How it works

```
Windows installer (.exe)
  └─ checks/enables WSL2
  └─ imports the "LIO-SAM-Studio" WSL distro (Ubuntu 24.04 + ROS 2 Jazzy + LIO-SAM + Python deps)
  └─ Start-menu shortcut  →  wsl -d LIO-SAM-Studio -- /opt/lio-sam/app/lio_sam_studio.sh
                                 └─ Tk GUI + RViz shown on the Windows desktop via WSLg
```

## Requirements (end users)

- **Windows 11 25H2 or 26H2** x64 (Windows 10 22H2 best effort while its ESU lasts, to 2027-10-12), with hardware virtualization enabled
- **WSL 2.4.8 or later** with WSLg (`wsl --version`; update with `wsl --update`)
- 16 GB RAM recommended (SLAM + RViz on large bags use 6–10 GB)
- ~10 GB disk for the distro, plus space for bags and maps
- For live Ouster recording: Windows 11 with WSL "mirrored" networking

Full version matrix: see [TODO.md](TODO.md#target-platforms-and-versions-reviewed-2026-11).

## Repository layout

| Path | Purpose |
|---|---|
| `upstream/` | Git submodule → `LIO-SAM-V2` (`ros2` branch). The application source; **not modified here**. |
| `image/` | Builds the WSL distro (`.wsl` package) from Ubuntu 24.04 + ROS 2 Jazzy + `upstream/` |
| `launcher/` | Windows-side launcher (shortcut target, drive mapping, first-run checks) |
| `installer/` | Installer definition (Inno Setup or WiX) |
| `docs/` | User and maintainer documentation |

## Getting the source

```bash
git clone --recurse-submodules https://github.com/band72/lio-sam-wsl.git
# update to the latest LIO-SAM-V2:
git submodule update --remote upstream
```

## Build platform

- **Image:** built on Linux (or inside WSL), producing a rootfs `.tar.gz`
- **Installer:** built on Windows

## Known limitations

- Reading large bags from `C:\` / `D:\` through WSL is much slower than from the distro's own disk
- RViz runs through WSLg's GPU translation layer; expect lower frame rates than native Linux
- Some corporate IT policies block virtualization, which rules this route out on those machines

## License and attribution

Based on **LIO-SAM** by Tixiao Shan et al. (IROS 2020), which is licensed under **BSD 3-Clause**
(© 2020 Tixiao Shan, © 2021 Christoph Gruber), and on LeGO-LOAM (BSD 3-Clause, © 2018 Robust Field Autonomy
Lab). Code derived from either stays under BSD 3-Clause; see [licenses/](licenses/). New code written for this
project is MIT (see [LICENSE](LICENSE)). Full details, the paper citations and BibTeX are in
[NOTICE.md](NOTICE.md).

If you use this project, please cite: T. Shan et al., "LIO-SAM: Tightly-coupled Lidar Inertial Odometry via
Smoothing and Mapping," IROS 2020, pp. 5135–5142,
[doi:10.1109/IROS45743.2020.9341176](https://doi.org/10.1109/IROS45743.2020.9341176).
