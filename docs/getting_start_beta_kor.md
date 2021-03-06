# 시작하기

이 문서는 한국 베타테스터를 위한 매뉴얼 문서입니다.

각 커맨드는 리눅스를 기준으로 작성되어 있습니다.

---
## 개요

베타테스터 여러분, 환영합니다.

XELNetwork는 (DYNAMI)XEL 프로토콜을 사용한 네트워크를 구축하기 위해 만들어졌습니다. 특히, 저렴한 시리얼 인터페이스(TTL/RS485) 기반으로 플러그 앤 플레이(약칭, PnP)를 가능케 함으로써 보다 편리한 개발방식을 시도해 보는 것이 1차 목표였습니다.
따라서, 베타테스터 패키지의 구성은 PnP기능을 위함이며 이는 ROS2의 토픽을 자동생성하는 예제를 포함하고 있습니다. 이 매뉴얼은 이를 위한 가이드문서이며, 유저가 이 외의 기능을 활용해보거나 직접 개발할 수 있는 환경 구축에 대한 내용을 포함하고 있습니다.

---

## 패키지 구성

- 베타테스터 패키지 구성
    - CommXEL-W * 1ea
    - PowerXEL * 1ea
    - SensorXEL * 1ea
    - JST 4핀케이블 * 2ea 
- 하드웨어 스펙 및 핀맵은 [**hardware**](hardware.md)페이지 참조

![](_static/xelnetwork_beta_pack.png)


---
## 의존성 설치

### ROS2
아래 문서를 참조하여 설치. (현재는 Dashing을 추천)
- [ROS2 설치 위키](https://index.ros.org/doc/ros2/Installation/Dashing/#installing-ros-2-dashing-diademata)

### Micro-XRCE-DDS Agent
ROS2(DDS, FastRTPS)와 통신하는데 사용.
자세한 내용은 ROS2의 DDS관련 자료 및 FastRTPS, XRCE-DDS 문서를 참조.

설치 방법에 대한 세부적인 내용은 [eProsima의 공식 매뉴얼](https://micro-xrce-dds.readthedocs.io/en/latest/installation.html#installing-the-agent-stand-alone)을 참조.
Agent 버전은 ROS2 버전(FastRTPS 버전)에 의존적이기 때문에, 사용하는 ROS2버전에 따라 Agent를 설치.
이와 관련된 내용은 [ros2arduino의 README](https://github.com/ROBOTIS-GIT/ros2arduino#version-specific-dependencies)를 참조.

아래는 베타테스터 패키지 펌웨어 및 ROS2 Dashing Patch4(혹은 ROS2 Eloquent) 기준.
```bash
$ sudo apt update
$ sudo apt install build-essential cmake git
# When you download this you must select the appropriate version.
$ git clone https://github.com/eProsima/Micro-XRCE-DDS-Agent.git -b 1.1.5
$ cd Micro-XRCE-DDS-Agent
$ mkdir build && cd build
$ cmake ..
$ make
$ sudo make install
$ sudo ldconfig /usr/local/lib/
```
---
## CommXEL-W 설정 (with XELNetwork Manager)
XELNetwork Manager라는 GUI툴을 통해 CommXEL-W의 설정 변경 가능.

### 다운로드 및 실행
```bash
$ git clone https://github.com/ROBOTIS-GIT/XelNetwork_Manager.git
```
- `XelNetwork_Manager/excutable`에서 자신의 OS에 맞는 폴더의 `XELNetwork_Manager` 실행
    - Windows: /win/XELNetwork_Manager.exe
    - Linux: /linux/XELNetwork_Manager
        - Linux의 경우, 포트(/dev/ttyUSBx)에 대한 권한을 허용해야 할 수 있습니다. 
        - `chmod` 혹은 `sudo usermod -a -G tty username` 사용할 것)
    - MacOS: 지원 안함
- 혹은 아래 커맨드 실행
    - Linux
```bash
  $ sudo usermod -a -G tty username    #필요한 경우 실행, username은 자신의 계정이름으로 변경
  $ cd ./XelNetwork_Manager/excutable/linux
  $ sudo chmod a+x ./XELNetwork_Manager
  $ ./XELNetwork_Manager
```

### 설정 변경하기
DYNAMIXEL의 컨트롤 테이블 방식.
![](_static/xel_manager.png)

- 기본 동작
    - 올바른 **Port**와 **Baud**(rate), **Protocol** 버전을 선택합니다.
    - **ID** 입력란에 올바른 ID(기본값 200)를 입력하고 [**Ping(ID)**] 버튼을 클릭하거나, ID와 상관 없이 모든 ID에 대해 검색을 하고 싶다면 [**Pings(All IDs)**] 버튼을 클릭합니다.
    - Ping에 대한 응답이 정상적으로 이루어졌다면, [**Ping**] 버튼 아래의 박스에 연결된 장치가 나타납니다.
    - 이 장치를 더블클릭하거나, 클릭 후 **Command** 박스의 [**ReadData**] 버튼을 클릭하여 컨트롤 테이블을 읽습니다.
    - 화면 왼쪽의 컨트롤 테이블에서 정보를 확인하거나 원하는 값으로 변경합니다.

- 무선 네트워크 설정 (필수)
    - Agent와 통신을 하기 위해, CommXEL-W의 무선 네트워크 설정을 해야 합니다.
    - **12~15**의 아이템들을 변경해야 합니다. (예시: 위 이미지)
    - 14번(주소 124)의 경우, 위에서 Agent를 설치한 PC의 IP주소를 입력합니다.

- 컨트롤 테이블

    |주소|크기(Byte)|명칭|내용|
    |:-:|:-:|:-:|:-:|
    |0|2|**Model Number**|DYNAMIXEL 프로토콜에서 컨트롤테이블을 구분하기 위해 사용하는 모델 번호|
    |2|4|**Model Info**|모델에 대한 추가 정보를 기록할 때 사용 (비활성화)|
    |6|1|**Firmware Version**|펌웨어 버전|
    |7|1|**ID**|DYNAMIXEL 프로토콜에서 사용되는 ID|
    |8|1|**Baudrate**|CommXEL-W의 USB와 연결되어 있는 UART의 속도. GUI및 Bypass모드에 영향을 주며, 이 값이 변경된다면 GUI Port의 Baud도 **동일하게** 설정해야 함|
    |10|32|**ROS2 node name**|ROS2에서 사용할 노드 이름 (`ros2 node list` 명령어로 확인 가능)|
    |50|1|**Auto scan start ID**|검색할 ID 범위의 시작값. PnP기능 사용 시, 주기적으로 CommXEL과 연결된 장치(SensorXEL, PowerXEL)를 검색하는데 사용됨|
    |51|1|**Auto scan end ID**|검색할 ID 범위의 마지막값. `Auto scan start ID`와 동일한 기능이며, 이 ID를 검색한 후 다시 `Auto scan start ID`부터 순차적으로 검색.|
    |52|4|**Auto scan interval(ms)**|PnP기능을 위한 자동 검색주기이며 단위는 milliseconds(ms). 이 주기마다 하나의 ID에 대해 검색|
    |58|1|**DXL Master baudrate**|CommXEL-W의 DYNAMIXEL포트의 속도. 연결된 장치들(SensorXEL, PowerXEL)과 동일해야 함|
    |59|1|**DXL Master protocol ver**|DYNAMIXEL 프로토콜의 버전. 연결된 장치들과 동일해야 함|
    |60|32|**WiFi AP SSID**|CommXEL-W가 연결할 AP(Access Point, 무선라우터)의 SSID|
    |92|32|**WiFi AP SSID P/W**|AP 접속 비밀번호|
    |124|16|**Micro-XRCE-DDS Agent IP**|Agent가 설치된 PC의 IP주소|
    |140|2|**Micro-XRCE-DDS Agent Port**|Agent에서 오픈한 Port번호|

---

## 하드웨어 연결

- Power(Max 24V) - PowerXEL - SensorXEL - CommXEL - USB(필요시)

![](_static/xelnetwork_board_connection.png)

---

## 데모 코드 동작 확인

### Agent 실행을 위한 기본설정파일 다운로드
- [GitHub 링크](https://raw.githubusercontent.com/ROBOTIS-GIT/XELNetworkMaster/master/xelnetwork_default.xml.refs)에서 다운 받거나 아래 명령 실행
```bash
$ wget https://raw.githubusercontent.com/ROBOTIS-GIT/XELNetworkMaster/master/xelnetwork_default.xml.refs
``` 
- 관련 세부 내용은 [여기](https://github.com/ROBOTIS-GIT/ros2arduino#appendix-how-to-configure-entities-from-reference-file-available-at-011-or-above)를 참조

### Agent 실행
- 위에서 기본 설정파일을 다운로드한 위치로 이동
- 네트워크 설정에 맞게 옵션주어 실행
```bash
  $ MicroXRCEAgent udp -p 2019 -r xelnetwork_default.xml.refs
```
- 자세한 사용법에 관해서는 아래 명령어 혹은 [`링크`](https://github.com/ROBOTIS-GIT/ros2arduino#appendix-how-to-configure-entities-from-reference-file-available-at-011-or-above) 확인
```bash
  $ MicroXRCEAgent -h
```

### ROS2에서 토픽 확인/전송
- 아래 명령을 통해 토픽 확인 (아래 화면과 같이 6개의 토픽 생성)
    - Publisher
        - **/Power_Current** from PowerXEL
        - **/Power_Voltage** from PowerXEL
        - **/XEL001_analog** from SensorXEL(ID: 1)
        - **/XEL001_gpio_in** from SensorXEL(ID: 1)
    - Subscriber
        - **/XEL001_gpio_out** to SensorXEL(ID: 1)
        - **/XEL001_pwm** to SensorXEL(ID: 1) (range: 0~4095)
```bash
$ ros2 topic list -t
```
![](_static/ros_topic_list.png)


- [ros2 CLI](https://github.com/ubuntu-robotics/ros2_cheats_sheet/blob/master/cli/cli_cheats_sheet.pdf) 명령을 통해 토픽 테스트
    - Publisher의 경우, `ros2 topic echo`명령
    - Subscriber의 경우, `ros2 topic pub` 명령

```bash
# echo 예시
$ ros2 topic echo /XEL001_analog

# pub 예시
$ ros2 topic pub /XEL001_gpio_out std_msgs/msg/Bool "data: 1"
```

---

## 부가 기능

### CommXEL-W 모드
CommXEL-W에는 두가지 모드가 존재

|모드|설명|진입방법|
|:-:|:-:|:-:|
|Plug and Play|기본 기능|User Button을 1초 미만(50ms 이상) 누른 후 떼기|
|Bypass|USB-DXL 데이터 바이패스|User Button을 1초 이상 누른 후 떼기|

![](_static/pinmap_commxel_w_rev_c.png)

USB기준 동작은 아래와 같음

|모드|USB 동작|
|:-:|:-:|
|Plug and Play|Config Tool을 통해 설정 변경 가능|
|Bypass|바이패스 통신, 속도는 1Mbps로 고정(업로드용)|

<br>

---

# 개발자 문서

## 참여 가능 레퍼지토리
|레퍼지토리|관련 사항|
|:-:|:-:|
|[ROBOTIS-GIT/Arduino_Core_STM32](https://github.com/ROBOTIS-GIT/Arduino_Core_STM32)|SensorXEL, PowerXEL|
|[ROBOTIS-GIT/arduino-esp32](https://github.com/ROBOTIS-GIT/arduino-esp32)|CommXEL-W|
|[ROBOTIS-GIT/XELNetworkMaster](https://github.com/ROBOTIS-GIT/XELNetworkMaster)|XELNetwork PnP의 마스터 기능을 위한 라이브러리|
|[ROBOTIS-GIT/XELNetworkSlave](https://github.com/ROBOTIS-GIT/XELNetworkSlave)|XELNetwork PnP의 슬레이브 기능을 위한 라이브러리|
|[ROBOTIS-GIT/Dynamixel2Arduino](https://github.com/ROBOTIS-GIT/Dynamixel2Arduino)|DYNAMIXEL 프로토콜 라이브러리|
|[ROBOTIS-GIT/ros2arduino](https://github.com/ROBOTIS-GIT/ros2arduino)|ROS2와 통신하기 위한 라이브러리|
|[ROBOTIS-GIT 내의 XELNetwork_* 레퍼지토리](https://github.com/ROBOTIS-GIT?utf8=%E2%9C%93&q=XELNetwork&type=&language=)|하드웨어, 로더, GUI, 문서 등| 

## 개발환경 구축
- 개발환경은 아두이노IDE를 지원
- 아두이노 설치 및 기본 사용법에 관해서는 아래 링크 참조
    - [아두이노IDE 설치](https://www.arduino.cc/en/Main/Software)
    - [아두이노 사용법](https://www.arduino.cc/en/Guide/HomePage)

### 보드 패키지 설치
- [공식 설치 방법 링크](https://www.arduino.cc/en/Guide/Cores)
<br>

- 3rd Party로 URL 추가하기
    - https://raw.githubusercontent.com/ROBOTIS-GIT/Arduino_Core_STM32/master/package_stm_xelnetwork_index.json
    - https://raw.githubusercontent.com/ROBOTIS-GIT/arduino-esp32/master/package_esp32_xelnetwork_index.json
![](_static/add_url_thirdparty.png)

- 보드 매니저에서 설치
    - "XELNetwork" 검색 후, 아래의 두 패키지 설치
![](_static/install_package_using_boards_manager.png)
![](_static/select_board.png)

### 라이브러리 설치
- [공식 설치 방법 링크](https://www.arduino.cc/en/guide/libraries)
<br>

- 라이브러리 매니저에서 설치 (공식 링크 참조)
    - ros2arduino
![](_static/ros2arduino.png)

- 수동 설치 (압축파일 방식, 공식 링크의 Manual installation 참조)
    - [Dynamixel2Arduino](https://github.com/ROBOTIS-GIT/Dynamixel2Arduino/releases/tag/0.4.0-xel)
    - [XELNetworkMaster](https://github.com/ROBOTIS-GIT/XELNetworkMaster)
    - [XELNetworkSlave](https://github.com/ROBOTIS-GIT/XELNetworkSlave)

### 의존성 설치
- (Linux) esp32 for XELNetwork (CommXEL-W)
```bash
$ sudo apt install python-pip
$ pip2 install pyserial  #dependancy for esptool.py
```

- (Linux) STM32 cores for XELNetwork (PowerXEL, SensorXEL)
```bash
$ cd <YOUR ARDUINO SKETCHBOOK FOLDER>/packages/STM32_XELNetwork/hardware/stm32/0.0.1/tools/xel_loader/linux
$ sudo chmod +x xel_loader  #excution permission for xel_loader
```


