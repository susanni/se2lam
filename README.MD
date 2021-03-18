se2lam
---
On-SE(2) Localization and Mapping for Ground Vehicles by Fusing Odometry and Vision

[![](../../workflows/Build/badge.svg)](../../actions?query=workflow%3A"Build")

### Related Publication

- Fan Zheng, Yun-Hui Liu. "Visual-Odometric Localization and Mapping for Ground Vehicles Using SE(2)-XYZ Constraints". _Proc. IEEE  International Conference on Robotics and Automation (ICRA)_, 2019 \[[pdf](https://fzheng.me/icra/2019.pdf)\] \[[poster](poster_fzheng.pdf)\]

  To cite it in bib:
  ```
  @inproceedings{fzheng2019icra,
      author    = {Fan Zheng and Yun-Hui Liu},
      title     = "{Visual-Odometric Localization and Mapping for Ground Vehicles Using SE(2)-XYZ Constraints}",
      booktitle = {Proc. IEEE Int. Conf. Robot. Autom (ICRA)},
      year      = {2019},
  }
  ```

  [![result in rviz](https://images.gitee.com/uploads/images/2019/0304/152353_36314cbb_874043.jpeg)](https://mycuhk-my.sharepoint.com/:v:/g/personal/1155051778_link_cuhk_edu_hk/EeIO3MJtH5pHsFkIRGHJbLEBRhRBGRRG6pwR19SFCrhQwQ?e=vbSLzS)

### Dependencies

- ROS (tested on Kinetic/Melodic)

- OpenCV 2.4.x / 3.1 above

- g2o ([2016 version](https://github.com/RainerKuemmerle/g2o/releases/tag/20160424_git))

#### Installing Dependencies

```bash
# Eigen (should install version 3.3.4-4)
sudo apt install libeigen3-dev

# g2o (2016)
git clone git@github.com:RainerKuemmerle/g2o.git
git checkout 20160424_git
cd g2o
mkdir build
cd build
cmake ../
sudo make install  # system-wide installation
```
When building `g2o `, the two warnings I got were "note: declared here
 EIGEN_DEPRECATED const unsigned int AlignedBit = 0x8" and something about "note: forward declaratiof of class ‘class g2o::World’”. `se2lam` still ran fine but any more warnings might be concerning.

### Build

Build this project as a ROS package

### Demo

1. Download [DatasetRoom.zip](https://mycuhk-my.sharepoint.com/:u:/g/personal/1155051778_link_cuhk_edu_hk/Ef4NuXvLZI1JhfljH9LkNxUB5xrDrCOrRnxwztO5bGKlew?e=U4aind), and extract it. In a terminal, `cd` into `DatasetRoom/`.

   We prepare two packages of odometry measurement data, one is more accurate (`odo_raw_accu.txt`), the other less accurate (`odo_raw_roug.txt`). To use either one of them, copy it to `odo_raw.txt` in `DatasetRoom/`.

2. Download [ORBvoc.bin](https://mycuhk-my.sharepoint.com/:u:/g/personal/1155051778_link_cuhk_edu_hk/EaF2ZkP17rdJrUHT0mrcf74Bl1h_691xZrxNILGbQbYFmA?e=nXRSS4).

3. Run rviz:

   ```
   roscd se2lam
   rosrun rviz rviz -d rviz.rviz
   ```

4. Run se2lam:
   
   ```
   rosrun se2lam test_vn PATH_TO_DatasetRoom PATH_TO_ORBvoc.bin
   ```

### Output

If running `test_vn`, the output is `se2lam_kf_trajectory.txt`, which is the final trajectory of keyframes. Each line contains `key_frame_id` `x` `y` `z` `yaw` (space deliminated) with units `none`, `mm`, `mm`, `mm`, `rad`. I'm pretty sure the keyframe ID is the same as the image number that created that keyframe, so if you know the timestamp of all of your images, you should know the timestamp of each line of the file.

### Dataset Format

The Dataset folder has to contain:
- `odo_raw.txt`: Odometry data with lines `x` `y` `yaw` (space deliminated) with units `mm`, `mm`, `rad`. Although not included in the file, the timestamp in which the odom data of each line of the txt file was obtained should be the same as the corresponding image in the `image` directory, i.e. line 0 of `odo_raw.txt` should be the odom data from the same timestamp in which `0.bmp` was captured.
- `image` directory: `.bmp` gray images named by sequential 0-indexed integers, i.e. `0.bmp`, `1.bmp`, `2.bmp`...
- `config` directory:
  - `CamConfig.yml`: calibration information of camera that captured the images in the `image` directory.
    - `camera matrix`: `K` matrix (camera intrinsics)
    - `distortion_coefficients`: `D` matrix (camera intrinsics)
    - `rvec_b_c`: rotation of camera with respect to vehicle expressed in Rodrigues angles (camera extrinsics)
    - `tvec_b_c`: translation of camera with respect to vehicle in mm (camera extrinsics)
  - `Settings.yml`: Unclear what most of this is used for, but important params:
    - `fps`: tracking frequency (I'm assuming this is frequency in which the odom and image data was captured.)
    - `img_num`: how many images/odom lines you have.
    - `write_map_file_path` and `read_map_file_path`: make sure these are paths that exist on your machine.
   
### Related Project

[izhengfan/se2clam](https://github.com/izhengfan/se2clam)  
[izhengfan/ORB_SLAM2](https://github.com/izhengfan/ORB_SLAM2)

### License 

[MIT](LICENSE)
