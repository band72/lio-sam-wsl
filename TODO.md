# TODO: lio-sam-wsl

Estimated effort: **~2–3 weeks**. Build platforms: Linux/WSL (image) and Windows 11 (installer).

## Target platforms and versions (reviewed 2026-11)

| Component | Target | Notes |
|---|---|---|
| Windows (primary) | **Windows 11 25H2 and 26H2**, x64 | 24H2 Home/Pro left servicing on 2026-10-13; 25H2 Home/Pro is serviced to 2027-10-12 |
| Windows (best effort) | Windows 10 22H2 x64 **with ESU** | Free consumer ESU runs to 2027-10-12; drop support after that |
| WSL | **WSL 2.4.8 or later** (`wsl --version`), WSLg enabled | Store-delivered WSL; required for `.wsl` packages and current WSLg |
| Distro base | **Ubuntu 24.04 LTS** | Required by ROS 2 Jazzy |
| ROS 2 | **Jazzy Jalisco (LTS, EOL May 2029)** | Same version as LIO-SAM-V2 |
| Architecture | x64 only for v1 | Windows on Arm: evaluate after v1 |
| Live recording networking | Windows 11 with WSL **mirrored networking** | NAT mode does not pass Ouster UDP traffic reliably |

## Phase 0: Project setup
- [ ] Pin `upstream/` to a tagged LIO-SAM-V2 release per version; version this repo as `vYYYY.MM.N`
- [ ] Add issue templates asking for `winver`, `wsl --version` and `wsl -l -v` output
- [ ] Turn on branch protection for `main` and require CI to pass

## Phase 1: Upstream portability prerequisites (PRs to LIO-SAM-V2, not changes here)
- [ ] Remove hard-coded paths in `lio_sam_gui.py` (`workspace_dir = /home/artwalk/...`); derive from the script location
- [ ] Make `BAGS_DIR` configurable (environment variable or settings file); default to the user's home
- [ ] Remove hard-coded paths in `run_survey.sh` and `record_survey.sh`
- [ ] Add a `requirements.txt` with pinned versions for the Python tools (numpy, scipy, rosbags, laspy[lazrs], pyproj)

## Phase 2: WSL distro image (`image/`)
- [ ] Script a reproducible build (Dockerfile or debootstrap): Ubuntu 24.04 → `ros-jazzy-desktop` → `libgtsam-dev` 4.2 → `colcon build` of `upstream/` → Python deps and `pcl-tools`
- [ ] Install the app to `/opt/lio-sam/`, with a non-root default user and `/etc/wsl.conf` (`[user] default=…`, `[boot] systemd=true` only if needed)
- [ ] Add **`/etc/wsl-distribution.conf`**: `[oobe] defaultName=LIO-SAM-Studio`, a shortcut icon, and a first-run script
- [ ] Ship as a **`.wsl` package** (rootfs tar renamed per Microsoft's custom-distro guide); keep `wsl --import` as the fallback
- [ ] Strip build tools, apt lists and caches; target under ~3 GB compressed; publish the SHA-256
- [ ] Smoke test: install the `.wsl`, launch the app, run the 90 s Ouster sample through Survey Processing

## Phase 3: Launcher (`launcher/`)
- [ ] Shortcut target running `wsl.exe -d LIO-SAM-Studio --cd ~ -- /opt/lio-sam/app/lio_sam_studio.sh` with no console window
- [ ] First-run checks with clear messages: Windows build, WSL ≥ 2.4.8 (offer `wsl --update`), virtualization enabled, WSLg working
- [ ] Recommend a `%UserProfile%\.wslconfig` memory setting (`memory=` at least 12 GB on 16 GB+ hosts); WSL defaults to 50% of host RAM, and SLAM plus RViz on large bags uses 6–10 GB
- [ ] Bag access: allow `/mnt/c`, `/mnt/d`; offer to copy large bags into the distro's ext4 disk (much faster than the 9P drive mount)
- [ ] App icon (`.ico`, 16–256 px) generated from `upstream/app/icons/lio-sam-studio.svg`

## Phase 4: Installer (`installer/`)
- [ ] Choose Inno Setup or WiX v4+; per-user install by default (no admin), with an admin step only to enable WSL
- [ ] Steps: detect or enable WSL (`wsl --install --no-distribution`, may need a reboot), install the distro, create Start-menu and desktop shortcuts
- [ ] Uninstall: `wsl --unregister LIO-SAM-Studio`, remove shortcuts; ask before deleting user maps
- [ ] Upgrades: keep maps and configs in `%UserProfile%\Documents\LIO-SAM` or a separate VHD so the distro can be replaced
- [ ] Code signing with **Azure Artifact Signing** (~$9.99/month) or an OV certificate; EV certificates no longer give instant SmartScreen trust, so plan for reputation to build over the first downloads

## Phase 5: Networking and live recording
- [ ] Document mirrored networking (`.wslconfig`: `[wsl2] networkingMode=mirrored`) and the Hyper-V firewall rules needed for Ouster UDP and ROS 2 DDS
- [ ] Test live recording with a real sensor, or mark recording as "Linux only" for v1

## Phase 6: CI, testing and release
- [ ] GitHub Actions: build the image on `ubuntu-24.04`, the installer on **`windows-2025`** (do not use `windows-2019`, retired 2025-06-30)
- [ ] Test matrix on clean VMs: Windows 11 25H2, Windows 11 26H2, Windows 10 22H2 (ESU)
- [ ] Performance check: conversion and SLAM time on the 27 GB sample bag, from `/mnt/*` versus inside the distro
- [ ] Release artifacts: `.wsl` package, signed installer, SHA-256 checksums, release notes
- [ ] `THIRD_PARTY_NOTICES.md` (Ubuntu, ROS 2 Apache-2.0, GTSAM BSD, PCL BSD, OpenCV Apache-2.0, Qt LGPL, Python packages)
- [ ] User guide in `docs/`: install, first run, importing a ROS 1 bag, saving and exporting maps

## Ongoing
- [ ] Re-test when a new Windows 11 annual release ships (each H2) and when WSL updates
- [ ] Re-base the image on the next ROS 2 LTS before Jazzy reaches end of life (May 2029)
