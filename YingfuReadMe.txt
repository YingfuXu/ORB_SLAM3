when modified the code, first ./build.sh and then ./build_ros.sh in ~/VIO/ORB_ws/src/ORB_SLAM3

rosbag play --pause MH_03_medium.bag /cam0/image_raw:=/camera/image_raw /imu0:=/imu
rosrun ORB_SLAM3 Mono_Inertial Vocabulary/ORBvoc.txt Examples/Monocular-Inertial/EuRoC.yaml

rosbag play --pause indoor_45_2_snapdragon_with_gt.bag /snappy_cam/stereo_l:=/camera/image_raw /snappy_imu:=/imu 
rosrun ORB_SLAM3 Mono_Inertial Vocabulary/ORBvoc.txt Examples/Monocular-Inertial/uzhfpv.yaml

indoor_forward_3_snapdragon_with_gt 9.50m/s Failed (even with ORBextractor.iniThFAST:5, ORBextractor.minThFAST:2)
indoor_forward_5_snapdragon_with_gt 4.87m/s Successed
indoor_forward_8_snapdragon_with_gt 5.26m/s Successed

indoor_45_1_snapdragon.bag 4.36 Successed on Second Half
indoor_45_3_snapdragon.bag 3.53 Successed on the later 2/3 part of sequence when the flight height become relatively high
(seems that lower ORBextractor.iniThFAST and ORBextractor.minThFAST do not help)
indoor_45_12_snapdragon.bag 4.33 initialized when flies high but lose tracking when fly close to the ground
failed on 2 4 13 14
The SLAM initializes the map very transitorily when the drone slows down to make turns and optical flow is relatively small. But it loses tracking very soon when the drone speeds up. We tried to lower the thresholds for the feature extraction but it makes little difference.

VINS-Fusion sometimes fails in the initialization and causes very big drifting. We also observed occasional unstable performance such as sharp turns in the estimated trajectories. So we run VINS-Fusion with the same setting and sequence several times until we get 2 or 3 good estimated trajectories, and the best one is shown in the table.
