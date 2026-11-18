# Notices and attribution

`lio-sam-wsl` is a derivative of **LIO-SAM** by Tixiao Shan et al. It builds on the ROS 2 port of LIO-SAM
through [LIO-SAM-V2](https://github.com/band72/LIO-SAM-V2), which this repository references as the
`upstream/` git submodule.

## Licenses

| Code | License | Copyright | Text |
|---|---|---|---|
| Original LIO-SAM and its ROS 2 port ([TixiaoShan/LIO-SAM](https://github.com/TixiaoShan/LIO-SAM)), and any code in this repository derived from it | **BSD 3-Clause** | © 2020 Tixiao Shan; © 2021 Christoph Gruber | [licenses/LIO-SAM-BSD-3-Clause.txt](licenses/LIO-SAM-BSD-3-Clause.txt) |
| Portions of LIO-SAM adapted from [LeGO-LOAM](https://github.com/RobustFieldAutonomyLab/LeGO-LOAM) | **BSD 3-Clause** | © 2018 Robust Field Autonomy Lab | [licenses/LeGO-LOAM-BSD-3-Clause.txt](licenses/LeGO-LOAM-BSD-3-Clause.txt) |
| New code written for this project | **MIT** | © 2026 band72 | [LICENSE](LICENSE) |

The original LIO-SAM is **not** MIT-licensed. Code copied or adapted from LIO-SAM or LeGO-LOAM stays under
BSD 3-Clause. Its copyright notices must be kept in the source files, and the license texts above must be
shipped with any source or binary release (installers included). The BSD 3-Clause license also prohibits
using the original authors' names to endorse or promote this project without their written permission.

## Citing

If you use this project, please cite the original LIO-SAM paper:

> T. Shan, B. Englot, D. Meyers, W. Wang, C. Ratti and D. Rus, "LIO-SAM: Tightly-coupled Lidar Inertial
> Odometry via Smoothing and Mapping," *2020 IEEE/RSJ International Conference on Intelligent Robots and
> Systems (IROS)*, 2020, pp. 5135–5142, doi: [10.1109/IROS45743.2020.9341176](https://doi.org/10.1109/IROS45743.2020.9341176).

```bibtex
@inproceedings{liosam2020shan,
  title={LIO-SAM: Tightly-coupled Lidar Inertial Odometry via Smoothing and Mapping},
  author={Shan, Tixiao and Englot, Brendan and Meyers, Drew and Wang, Wei and Ratti, Carlo and Rus, Daniela},
  booktitle={IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS)},
  pages={5135--5142},
  year={2020},
  organization={IEEE},
  doi={10.1109/IROS45743.2020.9341176}
}
```

Part of LIO-SAM is adapted from LeGO-LOAM:

```bibtex
@inproceedings{legoloam2018shan,
  title={LeGO-LOAM: Lightweight and Ground-Optimized Lidar Odometry and Mapping on Variable Terrain},
  author={Shan, Tixiao and Englot, Brendan},
  booktitle={IEEE/RSJ International Conference on Intelligent Robots and Systems (IROS)},
  pages={4758--4765},
  year={2018},
  organization={IEEE},
  doi={10.1109/IROS.2018.8594299}
}
```

GitHub's **"Cite this repository"** button uses [CITATION.cff](CITATION.cff), which points to the LIO-SAM paper.

## Third-party dependencies

Releases also bundle third-party libraries under their own licenses (for example GTSAM, PCL, OpenCV, Eigen,
ROS 2 and Qt). These are listed in `THIRD_PARTY_NOTICES.md` when release packaging is added; see
[TODO.md](TODO.md).
