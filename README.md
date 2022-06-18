# ORB_SLAM2
##  2022-1 Software Convergence Capstone Design

#### 과제 개요

###### 가. 과제 설계 배경 및 필요성

최근에 로봇 관련 사업이 성장하면서 그에 적용되는 기능들이 대두되고 있습니다. 그 중 하나는 SLAM으로 자신의 위치를 추정함과 동시에 지도를 작성하는 과정입니다. 최근 성장하는 만큼 중요한 시스템 중 하나라고 생각되기 때문에 SLAM 관련 주제를 시행하게 되었다. 

SLAM의 정의에 기재되어 있는 것처럼 가장 중요한 기능은 2가지이다. 자신의 위치를 추정하는 Localization과 동시에 지도를 작성하는 Mapping이다. 이 2가지를 동시에 동작하기 위해서는 생각보다 까다로운 과정들이 필요하다. 우리가 눈으로 바라보는 풍경들을 보며 그 풍경들을 기준으로 한 나의 위치를 파악하고 맵을 작성해본다고 생각하면 된다. 과연, 처음보는 풍경에서 한 쪽 눈을 가린채 그 풍경들까지의 거리를 파악하여 지도를 작성해 볼 수 있을까? 이와 관련해서 SLAM의 이론을 이해하고 직접 구현해봄으로써 조금 더 다가갈 수 있는 기회를 마련해본다.

  ![1](C:\Users\qjsro\Desktop\마크다운\1.png)



###### 나. 과제 주요내용

\1) SLAM이란?

동시적 위치 추정 및 지도 작성의 방법으로써, Simultaneous Localization And Mapping의 약자이다. 우선 자신의 위치를 파악하기 위해서는 자신이 보고 있는 시선들과 얼마나 떨어져있는지, 움직이면서 그 시선들이 어떻게 변하는지를 파악해야할 필요가 있다. 한마디로 지도가 어떻게 작성되어있는지 알아야 자신의 위치를 추정할 수 있다는 것이다. 마찬가지로 지도를 작성하기 위해서는 자신이 보고 있는 시선들이 어떤 위치에 놓여있는지 파악할 필요가 있다. 이것도 자신의 위치가 어디에 있는지 알아야 지도를 작성할 수 있다는 뜻이다. 마치 chicken-or-egg 논리와 비슷하다. 따라서 SLAM을 진행한다는 것은 간단한 일은 아니다.

SLAM의 종류에는 Visual SLAM, Visual Odometry SLAM, Lidar SLAM 등이 있는데, 이번 과제에서는 Visual SLAM에 대해서만 다룰 예정이다.

​    

\2) Visual SLAM이란?

카메라로 들어온 이미지만 활용하여 위치 추정 및 지도 작성을 하는 방법으로써, 적은 비용과 작은 장치를 이용하면 된다는 점에서 장점이 있다. 하지만 그만큼 성능이 저하될 수 있는 우려가 있기 때문에, 당연히 최종 목표는 비용을 더 절감하는 것보다는 현재의 상태에서 얼마나 더 좋은 성능을 낼 수 있는가에 대하여 있다. 예를 들어, Lidar Sensor의 경우 360도 회전하며 주위의 물체들을 바로 확인하여 정보를 수집할 수 있지만, 카메라를 이용하는 Visual SLAM의 경우에는 한 방향에서 바라볼 수 있는 field of view가 한정적이다. 또한, Monocular(단안 카메라), Stereo(양안 카메라), RGB-D(Depth 카메라)를 이용하여 진행하는 SLAM을 일컫는데, 직접적인 Depth를 구할 수 없는 Monocular SLAM의 경우에는 더욱 큰 정확도를 필요로 하게 된다. 이처럼 센서를 사용하지 않고 오로지 비용 절감의 목적으로 카메라만 이용하여 최대의 성능을 낼 수 있도록 하는 것이 Visual SLAM의 목표이다. 이번 과제에서는 Monocular를 이용한 SLAM에 대해서 파악해 볼 것이다.



###### 다. 최종결과물의 목표

\1) 체계적인 Visual SLAM 과정의 이해 및 평가

Visual SLAM에서 들어오는 정보는 카메라 화면이 전부이다. 이 카메라 화면이 어떻게 바뀌는지를 파악함으로써 자신의 위치 추정과 지도 작성을 동시에 해야한다. 더불어 Monocular를 이용한 SLAM인 경우에는 상대적인 Depth 거리도 파악을 해야한다. 이 과정은 꽤 체계적으로 이루어진다.

​    

​    ![2](C:\Users\qjsro\Desktop\마크다운\2.png)

​    

먼저 카메라를 통해 이미지 입력을 받는다. 받은 이미지를 토대로 가공을 해야하는데, 주로 특징점 추출을 통해 이미지의 특징을 뽑아내고 화면이 움직이는 동안 그 이미지의 특징점이 어떻게 움직이는지 파악을 하게 된다. 그러면서 3D 상의 점을 추정하고 최적화과정을 거친다. 그렇다면, 3D 상의 점은 어떻게 추정할까? 카메라의 내부 파라미터를 알고 있다는 전제하에 행렬을 통한 정합 과정을 이용하여 추정한다. 다음과 같은 과정을 거치면 2D 이미지를 통해 3D 상의 점 위치를 복원해낼 수 있는 것이다.



![4](C:\Users\qjsro\Desktop\마크다운\4.png) ![3](C:\Users\qjsro\Desktop\마크다운\3.png)

​    

\2) 효율적인 Visual SLAM 알고리즘 선정과 구현

Visual SLAM을 구현하는 여러 알고리즘들 중에서도 방법이 여러 가지이고 구현 절차가 모두 다르기 때문에 그 차이와 알고리즘을 선정하는 기준을 명확히 할 필요가 있다. 그렇기 때문에 다음과 같은 6가지의 기준을 정해두고 알고리즘을 선정할 수 있도록 한다.

​    

​    ![5](C:\Users\qjsro\Desktop\마크다운\5.png)

​    

이 6가지 알고리즘 선정 기준에 의거하여 Monocular를 이용한 SLAM에서의 Optimization 성능이 가장 뛰어난 ORB SLAM을 선정한다. 이 알고리즘의 구성을 파악하고 구현하기 위한 환경설정이 필요하다. 이것을 모두 구현해보는 것이 목표였으며, ORB SLAM의 경우 다음의 thread를 따른다.

​    

​    ![6](C:\Users\qjsro\Desktop\마크다운\6.png)

​    

크게 3가지 thread로 나뉜다. 카메라를 통해 프레임을 입력받은 뒤 Tracking 과정을 통해 특징점 추출 및 자신의 위치 추정 과정을 통해 새로운 키프레임을 결정하여 다음 thread로 넘겨 준다. 그 thread는 Local Mapping 과정이고, 입력받은 키프레임을 확인하며 특징점들을 3D 지도 상에 포인트 클라우드로 작성하고 움직이는 키프레임에 따라 겹치는 점들의 부분을 확인하며 최적화도 진행한다. 이후 Loop Closing thread를 통해 자신이 방문했던 위치인지 특징점들의 정보를 활용하여 판단한 뒤, 그 동안 쌓였던 오차들을 최적화하여 맵을 정리해주는 과정을 거친다. 이 과정을 모두 확인하는 것도 이번 과제의 목표이다.

\3) ORB SLAM의 문제점 고찰

이 부분은 수행 및 결과에서 언급하도록 하겠다.



#### 과제 수행 방법

준비물

\1) Remote PC : ROS를 실행할 수 있는 PC를 준비한다.

\2) 터틀봇3 와플 파이 : 카메라를 이용할 수 있는 터틀봇을 준비한다.

--> Remote PC에 ORB SLAM을 구동할 수 있는 환경을 마련한 뒤, 터틀봇과 통신하여 정보 연동

​    

PC 환경 설정

\1) Remote PC : Ubuntu 18.04 LTS + ROS Melodic

\2) 터틀봇3 : Ubuntu 18.04 LTS + ROS Melodic

--> 두 PC의 IP를 확인한 뒤, ROS MASTER URI를 설정함으로써 서로 통신이 가능하도록 만든다.

​    

​    ![7](C:\Users\qjsro\Desktop\마크다운\7.png)

​    ![8](C:\Users\qjsro\Desktop\마크다운\8.png)

​    

터틀봇3의 정보를 토픽으로 전달받아 Remote PC에서 설정할 수 있도록 한다. (조종)

\1) 터틀봇3 : $ roslaunch turtlebot3_bringup turtlebot3_robot.launch

\2) Remote PC : $ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch

​    

터틀봇3 와플파이의 경우 카메라를 라즈베리파이 전용 카메라를 사용하기 때문에 기존의 우분투에서 인식하는 usb camera와는 차이가 있다. 따라서 ROS 토픽으로 전송받기 위해서 이 카메라를 usb camera로 인식하도록 해야하기 때문에 해당 장치에 알맞은 드라이버와 디바이스 세팅이 필요하다. 이후 카메라로 들어오는 영상을 전송해 줄 수 있도록 패키지를 다운로드 받아 사용하면 된다. 이 과정은 보고서에서는 생략하겠다.

​    

가장 중요한 과정은 ORB SLAM 알고리즘을 사용할 수 있도록 환경을 설정해주고 빌드를 해 주는 것이다. 다행인 것은 오픈 소스로 공개가 되어있지만, 과거 자료이기 때문에 자신의 PC에 맞게 직접 코드들을 수정해줘야 하며, 빌드 오류 또한 많이 발생하기 때문에 일일이 수정해줘야 하며 가장 중요함과 동시에 시간 소요가 많은 작업이다. (https://github.com/raulmur/ORB_SLAM2)

​    

이후 앞선 작업에서 진행한 터틀봇3와 Remote PC의 연동을 통해 정보를 주고 받을 수 있게 되었기 때문에, 터틀봇3에서 받아들이는 정보를 이용하여 ORB SLAM 알고리즘에 입력으로 넣어주고 SLAM을 진행하면서 결과를 저장할 수 있도록 한다. 이 결과를 토대로 어떠한 기능을 수행하는지와 문제점을 파악하며 결론을 도출해낼 수 있다.

​    ![9](C:\Users\qjsro\Desktop\마크다운\9.png)



##### 수행 결과

###### 과제 수행 결과

\1) Viewer + Webcam을 이용한 ORB SLAM

​    ![10](C:\Users\qjsro\Desktop\마크다운\10.png)

​    

\2) Rviz + 터틀봇3을 이용한 ORB SLAM

​    ![그림1](C:\Users\qjsro\Desktop\마크다운\그림1.gif)

​    

###### 최종결과물 주요특징 및 설명

우선 1차적으로 Webcam을 이용하여 Viewer 상에 어떤 식으로 나타나는지 확인을 한 뒤, 터틀봇3 카메라와 연동하여 Rviz를 이용하여 ROS를 통해 지도를 확인하였다.

​    

\1) Viewer + Webcam을 이용한 ORB SLAM

Pangolin이라는 ORB SLAM과 연동되는 openGL 라이브러리를 통해 Viewer로 실시간으로 작성되는 지도를 확인할 수 있다. 따라서, 웹캠을 이용해서 가볍게 책상 위 물체들을 비춰주었고 그에 따라 책상 위의 물건, 키보드, 모니터, 책장 등이 점들로 표시되어 지도가 작성되는 것을 확인할 수 있다. 단지 9000원짜리 카메라 하나로 받은 영상 입력을 토대로 3D 지도를 작성하게 되는 것이다.

​    

\2) Rviz + 터틀봇3을 이용한 ORB SLAM

뚜렷한 방들의 모양이 보이지 않아 잘못된 지도라고 생각할 수 있지만 어느 정도는 인정하지만 그렇지 않다. 우선은 터틀봇3 조종에 한계가 있다는 점(예를 들어, Field of view 등)과 구현을 한 공간이 매우 좁다는 점을 고려할 수 있다. 그리고 군데군데 점들이 뚜렷하게 나타난 공간도 있는데 터틀봇3 즉, 카메라와 근접했을 때는 더욱 촘촘한 특징점들을 구성하고 맵핑을 하기 때문에 그런식으로 나타날 수 있으며, 터틀봇3의 속도를 줄이고 시간을 늘리면 더욱 dense한 맵핑이 가능하다. 그럼에도 많이 나타나는 outlier에 대해서는 개선해야 할 여지가 있다.

​    

한 가지 특이한 점은 터틀봇이 집 안을 한 바퀴 돌고 나서 다시 제자리로 왔을 때, 기존에 출발했었던 위치인 만큼 정확히 Localization이 이루어졌다면 이미 작성된 지도로 돌아왔어야 하지만, 새로운 공간에 같은 물체에 대한 맵핑을 또 하고 있었다. 하지만 그 걱정이 무색하게 곧바로 자신이 왔었던 위치라는 것을 인지하고 어디인지 파악을 해서 Loop Closing을 진행하는 모습을 볼 수 있었다.

​    

​    ![12](C:\Users\qjsro\Desktop\마크다운\12.png)



##### 기대효과 및 활용방안

###### 기대효과

단안 카메라를 사용하는 SLAM에 대한 장점 및 문제점을 파악하고, 개선해야 할 부분에 대한 고찰을 할 수 있는 여지가 생겼다. 이러한 문제점들을 개선하게 되면 예를 들어 인간이 접근할 수 없는 구역에 대해서 어떠한 지형과 모습을 가지고 있는지 파악할 수 있는 능력이 더욱 높아질 것이다. 또한, 로봇 자체만으로 자의적으로 움직이는 것에도 크게 도움이 될 것이다.

###### 활용방안

가장 cost가 낮은 수단인 단안 카메라를 사용하여 얼마나 정확성을 높일 수 있는지에 대한 연구가 가능하다. 비용이 낮고 작은 부피만 사용하면 되는 만큼 정확성을 다른 센서와 맞먹을 정도로 연구가 성행하면 더욱 대중화될 수 있는 제품이 만들어 질 것이다.



##### 결론 및 제언

단안 카메라를 사용하여 ORB SLAM을 구현하는 경우 다음과 같은 부분들을 확인할 수 있었다.

\1) Loop Closing : 이전에 방문한 위치를 visual하여 확인하는 경우, 지도 최적화

\2) Textureless 공간에서는 tracking이 원활하게 이루어지지 않는다는 문제점

\3) Depth 정보를 알 수 없기 때문에 대상과의 실제 거리를 파악할 수 없다는 문제점

\4) 바닥 등 잘못 추적된 feature에 의해 bad point가 생성되는 문제점

이외, SLAM의 기능들을 직접 구현해봄으로써 파악하고 후행 연구를 위한 기반을 마련했다.



##### 참고자료

\1) 2022_robotics_A Comprehensive Survey of Visual SLAM Algorithms

\2) 2015_robotics_ORB-SLAM: a Versatile and Accurate Monocular SLAM System

\3) https://github.com/raulmur/ORB_SLAM2

\4) https://github.com/skylook/ORB_SLAM2-gridmap/blob/master/2d-grid-mapping.pdf
