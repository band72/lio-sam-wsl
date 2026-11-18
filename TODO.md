# TODO: lio-sam-wsl

Estimated effort: **~2–3 weeks**. Build platforms: Linux/WSL (image) and Windows (installer).

## Phase 0: Project setup
- [ ] Create the public GitHub repo `band72/lio-sam-wsl` and push this scaffolding
- [ ] Decide versioning (e.g. `vYYYY.MM.N`) and pin `upstream/` to a tagged LIO-SAM-V2 release per version
- [ ] Add issue templates (bug report should ask for Windows build, WSL version and `wsl --version` output)

## Phase 1: Upstream portability prerequisites (PRs to LIO-SAM-V2, not changes here)
- [ ] Remove hard-coded paths in `lio_sam_gui.py` (`workspace_dir = /home/artwalk/...`); derive from the script location
- [ ] Make `BAGS_DIR` configurable (environment variable or settings file); default to the user's home
- [ ] Remove hard-coded paths in `run_survey.sh` and `record_survey.sh`
- [ ] Add a `requirements.txt` for the Python tools (numpy, scipy, rosbags, laspy[lazrs], pyproj)

## Phase 2: WSL distro image (`image/`)
- [ ] Script the image build: Ubuntu 24.04 base → ROS 2 Jazzy (`ros-jazzy-desktop`) → `libgtsam-dev` (4.2) → `colcon build` of `upstream/`
- [ ] Install Python dependencies and `pcl-tools` (for `pcl_pcd2ply`)
- [ ] Install the app to `/opt/lio-sam/` with a non-root default user
- [ ] Strip build tools and caches; target an image under ~3 GB compressed
- [ ] Export with `wsl --export` (or build the rootfs directly) to a `.tar.gz`; record its SHA-256
- [ ] Smoke test: `wsl --import`, launch the app, run the 90 s Ouster sample through Survey Processing

## Phase 3: Launcher (`launcher/`)
- [ ] Shortcut target that starts the distro and runs `lio_sam_studio.sh` (no console window)
- [ ] First-run check: WSL2 installed and version ≥ 2.x, WSLg available; friendly error otherwise
- [ ] File access: let users pick bags on Windows drives (`/mnt/c`, `/mnt/d`); offer to copy large bags into the distro for speed
- [ ] App icon (`.ico`) converted from `upstream/app/icons/lio-sam-studio.svg`

## Phase 4: Installer (`installer/`)
- [ ] Choose Inno Setup or WiX
- [ ] Install steps: check/enable WSL (may need a reboot), `wsl --import LIO-SAM-Studio`, create Start-menu and desktop shortcuts
- [ ] Uninstaller: `wsl --unregister LIO-SAM-Studio`, remove shortcuts (ask before deleting user maps)
- [ ] Upgrade path: replace the distro while keeping user data (maps and configs in a separate folder)
- [ ] Code signing certificate (~$200–500/year) to avoid SmartScreen warnings

## Phase 5: Networking and live recording
- [ ] Document Windows 11 mirrored networking (`.wslconfig`: `networkingMode=mirrored`) for Ouster UDP
- [ ] Windows Firewall rules for the Ouster ports and ROS 2 DDS
- [ ] Test live recording with a real sensor, or mark recording as "Linux only" for v1

## Phase 6: Testing and release
- [ ] Test on clean Windows 10 22H2 and Windows 11 virtual machines
- [ ] Performance check: conversion and SLAM time on the 27 GB sample bag, on `/mnt/*` versus inside the distro
- [ ] GitHub Actions: build the image on Linux and the installer on `windows-latest`; attach both to releases
- [ ] `THIRD_PARTY_NOTICES.md` listing bundled licenses (Ubuntu, ROS 2 Apache-2.0, GTSAM BSD, PCL BSD, OpenCV Apache-2.0, Qt LGPL, Python packages)
- [ ] User guide in `docs/`: install, first run, importing a ROS 1 bag, saving and exporting maps
