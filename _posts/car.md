---
layout: post
title:  "자율주행 자동차"
summary: "한동대학교 자율주행 자동차"
date:   2019-06-24 12:00 -0400
categories: contest
---

# 한동대 자율주행 자동차 대회

장소 : 한동대학교
날짜 : 2019-06-24 ~ 2019-06-26

# Dependency
- ROS
- raspberry pi 3 B+ (ubuntu mate)

# SSH 설정

```
sudo apt-get install ssh
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
```

# 비밀번호 변경

```
sudo passwd root
새 암호 입력
새 암호 재입력
```

---

# ROS(Robot Operation System)
로봇 소프트웨어를 개발하기 위한 소프트웨어 프레임워크

하나의 프로그램은 노드다. 노드간의 메세지 교환 방법을 이용해 복잡한 프로그램을 잘게 나눠 공동 개발이 가능하다.

```
ROBOT ㅡㅡ 메타 운영체제 ㅡㅡ Sensor
               |
               |
              APP
```

## 설치

```
sudo apt-get update
sudo apt-get upgrade
wget https://raw.githubusercontent.com/ROBOTIS-GIT/robotis_tools/master/install_ros_kinetic.sh && chmod 755 ./install_ros_kinetic.sh && bash ./install_ros_kinetic.sh
```

## camera test

```
cm -ji
one terminal : roscore
second terminal : rosrun camera_show camera_show_node
```
