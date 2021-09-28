when modified the code, first ./build.sh and then ./build_ros.sh in ~/VIO/ORB_ws/src/ORB_SLAM3
注意：需要在/VIO/ORB_ws/src/ORB_SLAM3 中开启terminal来加载bashrc中写入的相对路径
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
failed on 2 4 9 13 14

The SLAM initializes the map very transitorily when the drone slowed down to make turns and optical flow was relatively small. But it lost tracking very soon when the drone speeded up. We tried to lower the thresholds for the feature extraction but it made little difference.

We used the IMU noise parameters given by the UZH-FPV dataset when evaluating ORB-SLAM3.

We observed occasional big drifting in the estimated trajectories of VINS-Fusion. So we run VINS-Fusion several times on each sequence until we get 3 or more good estimated trajectories, and the best one is shown in the table.
We used the IMU noise parameters given by the UZH-FPV dataset when evaluating VINS-Fusion.

in VINS_Fusion: max number of features in tracking was set to 300 to be the same as OPENVINS.
#imu parameters       The more accurate parameters you provide, the better performance
acc_n: 2.236067977          # accelerometer measurement noise standard deviation. 
gyr_n: 1.118033989         # gyroscope measurement noise standard deviation.     
acc_w: 0.04472136        # accelerometer bias random work noise standard deviation.  
gyr_w: 8.94427e-04      # gyroscope bias random work noise standard deviation. 
datasheet value multiply sqrt(500)

ROVIO: At the beginning of each sequence of the UZH-FPV dataset, the drone was swung by the human operator and then put on the ground. For sequences 2, 9, and 12, when we started ROVIO at the very beginning of the sequences, the estimated trajectories showed huge drifts.  By tuning the starting time of ROVIO between after the drone being swung and before the drone taking off, we obtained much better results on these sequences.

ROVIO: We tried the IMU noise parameters given by the UZH-FPV dataset and the parameters in the ROVIO's configuration file for EuRoC. After several attempts, we found that the combination of ROVIO's IMU noise densities for EuRoC and the IMU random walk parameters given by the UZH-FPV dataset yielded good results.

We tried to get good (perhaps not the best) results from the open-sourced VIO algorithms by tuning their parameters (e.g. IMU noise density) and the starting time of the dataset sequences.

LARVIO multiple runs and seq9 need to start at the beginning others after hand winging
LARVIO param: https://fpv.ifi.uzh.ch/wp-content/uploads/sourcenova/uni-comp/2019-2020-uzh-fpv-benchmark/submissions/20/details.pdf
IMU noise: same as LARVIO EuRoC.yaml

