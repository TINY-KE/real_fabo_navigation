a) 键盘控制机器人运动

将cmd_vel转化为左右轮速度，并通过蓝牙发送给fabo机器人

bash
Copy code
rosrun fabo_robot motion_to_bluetooth
启动键盘控制器，话题为/turtle1/cmd_vel

bash
Copy code
rosrun turtlesim turtle_teleop_key
b) 注意：对于真实场景的机器人的cartographer，use_sim_time设置为false；gazebo中设置为true。

c) 建图：

启动雷达【内部包含了雷达和机器人的tf变换】

bash
Copy code
roslaunch urg_node urg_lidar.launch 
启动没有里程计的cartographer建图

bash
Copy code
roslaunch cartographer_ros hokuyo_no_odom.launch
完成轨迹，不再接受新的数据:

bash
Copy code
rosservice call /finish_trajectory 0
序列化保存当前状态, 保存为pdstream格式:

bash
Copy code
rosservice call /write_state "{filename: '/home/zhjd/ws_real_fabo_navigation/src/real_fabo_navigation/carto_map_save/map_name.pbstream'}"
d) 导航

启动蓝牙

蓝牙硬件重启

bash
Copy code
sudo systemctl daemon-reload 
sudo systemctl restart bluetooth 
sudo chmod 777 /var/run/sdp
sdptool add --channel=22 SP
连接蓝牙：小胖的安卓平板连接自己的笔记本

让PC端等待连接:

bash
Copy code
sudo rfcomm listen /dev/rfcomm0 22
在安卓app中点击“连接设备”，选中对应自己的笔记本

赋予蓝牙串口权限：

bash
Copy code
sudo chmod 777 /dev/rfcomm0
运行：hokuyo10_navigation_cartographer.launch

注意修改其中的地图的位置