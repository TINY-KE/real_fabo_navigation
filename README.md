### fabo机器人的建图导航

## 一、键盘控制机器人运动
+ 将cmd_vel转化为左右轮速度，并通过蓝牙发送给fabo机器人
    `rosrun fabo_robot motion_to_blueteeth`

+ 启动键盘控制器，话题为/turtle1/cmd_vel
    `rosrun turtlesim turtle_teleop_key`

## 二、注意的问题：
+ 对于真实场景的机器人的cartographer，use_sim_time设置为false；gazebo中设置为true。
+ 在BtManager.cpp的compute()函数中，可以修改“轮间距”和“速度的修正系数”
    按理说，fabo机器人出厂时已经标定过里程计了，直接发送“速度”就可以得到比较准确的移动效果。但是课题三的小胖机器人装了手臂，所以我们又标定了下，给左右轮速度加了个“修正系数”。
+ 在fabo_robot/config/fabo/base_local_planner_params3.yaml中，修改局部路径规划发布/cmd_vel的频率，目前为5
  + 发布/cmd_vel的频率，不能过高。超过10，很容易导致安卓平板死机。
  + 不确定是蓝牙的问题，还是安卓平板的性能问题。可以尝试通过更换为wifi通信的方式。
+ 
  
## 三、建图：
+ 启动雷达【内部包含了雷达和机器人的tf变换】

    `roslaunch urg_node urg_lidar.launch `

+ 启动没有里程计的cartographer建图

    `roslaunch cartographer_ros hokuyo_no_odom.launch`

+ 完成轨迹  不再接受新的数据:

    `rosservice call /finish_trajectory 0`

+ 序列化保存当前状态,保存为pdstream格式:

    `rosservice call /write_state "{filename: '/home/zhjd/ws_real_fabo_navigation/src/real_fabo_navigation/carto_map_save/map_name.pbstream'}"`

## 四、导航
+ 启动蓝牙

  + 蓝牙硬件重启

        sudo systemctl daemon-reload 
        sudo systemctl restart bluetooth 
        sudo chmod 777 /var/run/sdp
        sdptool add --channel=22 SP

  + 连接蓝牙：小胖的安卓平板连接自己的笔记本

  + 让PC端等待连接:  

    `sudo rfcomm listem /dev/rfcomm0 22`

  + 在安卓app中点击“连接设备”，选中对应自己的笔记本

  + 赋予蓝牙串口权限：

    `sudo chmod 777 /dev/rfcomm0`

+ 运行：注意修改其中的地图的位置

    `hokuyo10_navigation_cartographer.launch`