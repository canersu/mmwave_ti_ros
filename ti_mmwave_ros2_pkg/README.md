미심쩍은 부분

```
void DataUARTHandler::start(void)
```

여기서 spin을 걸어뒀는데 나는 node안에 node를 만들어서 실행하도록 해두었다.

```
    auto DataHandler = std::make_shared<DataUARTHandler>();
    DataHandler->setFrameID( (char*) myFrameID.c_str() );
    DataHandler->setUARTPort( (char*) mySerialPort.c_str() );
    DataHandler->setBaudRate( myBaudRate );
    DataHandler->setMaxAllowedElevationAngleDeg( myMaxAllowedElevationAngleDeg );
    DataHandler->setMaxAllowedAzimuthAngleDeg( myMaxAllowedAzimuthAngleDeg );
    
    rclcpp::spin(DataHandler);
```

```
$ ros2 component types
...
ti_mmwave_ros2_pkg
  ti_mmwave_ros2_pkg::mmWaveDataHdl

# terminal 1
ros2 run rclcpp_components component_container
ros2 component list
/ComponentManager

# terminal 2
ros2 component load /ComponentManager ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg::mmWaveDataHdl

ros2 component load /ComponentManager ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg::mmWaveCommSrv

ros2 component unload /ComponentManager 1
```

```
ros2 launch ti_mmwave_ros2_pkg 
```

* Quick Config만

```
ros2 launch ti_mmwave_ros2_pkg eloquent_composition.launch.py
=> 알아서 잘 꺼짐

ros2 run rclcpp_components component_container
ros2 component load /ComponentManager ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg::mmWaveCommSrv

ros2 component load /my_container ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg::mmWaveCommSrv


```

* mmWaveDataHdl
```
ros2 run rclcpp_components component_container

ros2 launch ti_mmwave_ros2_pkg eloquent_only_config.launch.py

ros2 component load /ComponentManager ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg::mmWaveDataHdl

ros2 component load /ComponentManager ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg::mmWaveCommSrv

ros2 component unload /ComponentManager 1

```

결과

```
==============================
DataUARTHandler Read Thread joined
DataUARTHandler Sort Thread joined
DataUARTHandler Swap Thread joined
[INFO] [mmWaveDataHdl]: mmWaveDataHdl: Finished onInit function
DataUARTHandler Read Thread: Port is open[INFO] [ComponentManager]: Found class: rclcpp_components::NodeFactoryTemplate<ti_mmwave_ros2_pkg::ParameterParser>
[INFO] [ComponentManager]: Found class: rclcpp_components::NodeFactoryTemplate<ti_mmwave_ros2_pkg::mmWaveCommSrv>
[INFO] [ComponentManager]: Instantiate class: rclcpp_components::NodeFactoryTemplate<ti_mmwave_ros2_pkg::mmWaveCommSrv>
[INFO] [mmWaveCommSrv]: mmWaveCommSrv: command_port = /dev/ttyUSB0
[INFO] [mmWaveCommSrv]: mmWaveCommSrv: command_rate = 115200
[INFO] [mmWaveCommSrv]: mmWaveCommsrv: Finished onInit function
component_container: ../nptl/pthread_mutex_lock.c:81: __pthread_mutex_lock: Assertion `mutex->__data.__owner == 0' failed.

```

```
sudo chmod 666 /dev/ttyUSB0
sudo chmod 666 /dev/ttyUSB1

ros2 run ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg
=> 이거 하면 
DataUARTHandler Read Thread: Port is opensyncedBufferSwap
이거까지는 나온다.

ros2 run ti_mmwave_ros2_pkg mmwave_comm_srv_node
ros2 launch ti_mmwave_ros2_pkg eloquent_only_config.launch.py

ros2 param list
```

* launch 파일 만들기
```
ros2 launch ti_mmwave_ros2_pkg eloquent_composition.launch.py
```
* parameter 바꿀 수 있게 변경

> config에서 미리 셋업을 하고 생성도 한 다음, 그걸 ti_mmwave_ros2_pkg가 받아서 사용하도록 한다.
> 대신 한번 바꾸면 다시 또 바꿀 수는 없음
```
ros2 run ti_mmwave_ros2_pkg mmwave_comm_srv_node
ros2 launch ti_mmwave_ros2_pkg eloquent_only_config.launch.py

ros2 run ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg
```

* rviz
```
ros2 launch ti_mmwave_ros2_pkg eloquent_composition.launch.py
use sim time true로 하면 점이 안사라진다.
```

* 이 모든 것을 한번에!

```
ros2 launch ti_mmwave_ros2_pkg eloquent_composition.launch.py
```

[Multi Sensor] Debug
```
/home/kimsooyoung/ros2_ws/install/ti_mmwave_ros2_pkg/lib/ti_mmwave_ros2_pkg/mmWaveQuickConfig /home/kimsooyoung/ros2_ws/install/ti_mmwave_ros2_pkg/share/ti_mmwave_ros2_pkg/cfg/6843ISK_3d.cfg --ros-args -r __node:=mmWaveQuickConfig -r __ns:=/radar_0

ros2 run ti_mmwave_ros2_pkg mmwave_comm_srv_node --ros-args -r __ns:=/radar_0 -p "namespace:=radar_0"

ros2 run rclcpp_components component_container

ros2 component load /ComponentManager ti_mmwave_ros2_pkg ti_mmwave_ros2_pkg::mmWaveDataHdl -p namespace:="radar_0"
```

* multi sensor

```
ros2 launch ti_mmwave_ros2_pkg eloquent_multi0_composition.launch.py
ros2 launch ti_mmwave_ros2_pkg eloquent_multi1_composition.launch.py
ros2 launch ti_mmwave_ros2_pkg eloquent_multi_only_rviz.py
```

```
==============================
List of parameters
==============================
Number of range samples: 240
Number of chirps: 16
f_s: 7.500 MHz
f_c: 62.300 GHz
Bandwidth: 3200.000 MHz
PRI: 81.000 us
Frame time: 33.333 ms
Max range: 11.242 m
Range resolution: 0.047 m
Max Doppler: +-4.951 m/s
Doppler resolution: 0.619 m/s
==============================
```
