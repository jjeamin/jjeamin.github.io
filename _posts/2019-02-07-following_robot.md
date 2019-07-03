---
layout: post
title:  "Following Robot"
summary: "Cascade Training을 이용한 Following Robot using Raspberry"
date:   2019-02-07 12:00 -0400
categories: pi
---

[[깃허브](https://github.com/jjeamin/Raspi_following_Robot)]

# Dependency
- Raspberry PI 3(라즈비안)
- DC 모터 2개
- L293D
- PI camera

# requirement
- python 2.7
- opencv 4.0.0 [[<span style="color:blue">다운로드 방법</span>]](https://webnautes.tistory.com/916)
- RPI.GPIO
- LBP Cascade

```
라즈비안 설치와 원격 설정과 같은 기본적인 설정은 완료되었다는 가정하에 진행합니다.
```

---

## 0. git
git이 없다면 설치해줍니다.
```
sudo apt-get install git-core
git config --global user.name “NewUser"
git config --global user.email newuser@example.com
```
소스코드 clone
```
git clone https://github.com/jjeamin/Raspi_following_Robot.git
```



## 1. 라즈베리파이 GPIO



![GPIO](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/human_following_robot/gpio.JPG)



라즈베리파이 GPIO를 이용해 모터를 동작시킵니다.



![dc](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/human_following_robot/dc.JPG)



출처:[https://becominghuman.ai/building-self-driving-rc-car-series-2-hardware-setup-5e11eff3da3a](https://becominghuman.ai/building-self-driving-rc-car-series-2-hardware-setup-5e11eff3da3a)

위와 같은 방식으로 라즈베리파이와 DC모터를 결합합니다. L293D에서 **보라색선과 파란색선** 를 어디에 꽂았는지를 기억해 주어야합니다.

## 2. test/motor_test.py
모터가 잘동작하는지 테스트해줍니다.(항상 테스트가 중요한거 같습니다...)
자신이 보라색선 오른쪽 맨 끝에 꽂은 번호를 Motor1E에 적고 나머지를 왼쪽부터 차례대로 Motor1A,Motor1B에 적습니다. (파란색선은 반대 입니다)

**혹시 예상하는 방향으로 가지않는다면 정회전과 역회전을 변경해주면서 맞춰줍니다.**
- MotorA = 정회전
- MotorB = 역회전
- MotorE = ON/OFF

```python
import cv2
import RPi.GPIO as GPIO
from time import sleep

GPIO.setmode(GPIO.BCM)

Motor1A = /
Motor1B = /
Motor1E = /

Motor2A = /
Motor2B = /
Motor2E = /

GPIO.setup(Motor1A,GPIO.OUT)
GPIO.setup(Motor1B,GPIO.OUT)
GPIO.setup(Motor1E,GPIO.OUT)

GPIO.setup(Motor2A,GPIO.OUT)
GPIO.setup(Motor2B,GPIO.OUT)
GPIO.setup(Motor2E,GPIO.OUT)

print "Going forwards"
GPIO.output(Motor1A,GPIO.HIGH)
GPIO.output(Motor1B,GPIO.LOW)
GPIO.output(Motor1E,GPIO.HIGH)

GPIO.output(Motor2A,GPIO.HIGH)
GPIO.output(Motor2B,GPIO.LOW)
GPIO.output(Motor2E,GPIO.HIGH)

sleep(2)

print "Going backwards"
GPIO.output(Motor1A,GPIO.LOW)
GPIO.output(Motor1B,GPIO.HIGH)
GPIO.output(Motor1E,GPIO.HIGH)

GPIO.output(Motor2A,GPIO.LOW)
GPIO.output(Motor2B,GPIO.HIGH)
GPIO.output(Motor2E,GPIO.HIGH)

sleep(2)

print "Now stop"
GPIO.output(Motor1E,GPIO.LOW)
GPIO.output(Motor2E,GPIO.LOW)

GPIO.cleanup()
```

## 3. PI camera
설정방법 :  [https://webnautes.tistory.com/929](https://webnautes.tistory.com/929)

## 4. test/picamera_test.py
```python
import cv2

cam = cv2.VideoCapture(-1)

while(True):
	ret, frame = cam.read()
	cv2.imshow('image',frame)

	k = cv2.waitKey(10) & 0xFF
	if k == 27:
		break

cam.release()
cv2.destroyAllWindows()
```
- 웹캠
```python
cam = cv2.VideoCapture(0)
```
- PI camera
```python
cam = cv2.VideoCapture(-1)
```

## 5. Cascade classifier
- Cascade classifier란 긍정데이터와 부정데이터를 학습시켜 특징을 찾아내는 분류기법이다.

- Haar Cascade
  + LBP에 비해서 정확도가 높습니다.
  + LBP에 비해서 속도가 느립니다.
  + 영역간의 밝기차를 이용합니다.

- LBP Cascade
  + Haar에 비해서 정확도가 낮습니다.
  + Haar에 비해서 속도가 빠릅니다.
  + 영역의 상대적인 밝기차를 2진수로 코딩한 인덱스입니다.
  + 우리는 이것을 사용합니다.

## 6. human_following_robots.py
haar 폴더에 인터넷에 기본적으로 제공되는 학습된 cascade 파일이 들어있습니다. 각자 상황에 맞게 사용하지면 됩니다.(눈, 하반신, 상반신, 얼굴 등)
```
cascade = cv2.CascadeClassifier('./haar lbpcascade_frontalface_improved.xml')
```  
코드를 요약해서 설명하자면 cascade를 이용해서 가장 앞에있는 얼굴영역을 추출하고 얼굴영역의 중심과 캠의 중심의 위치 차이를 이용해서 로봇을 움직입니다.

+스레딩 추가
```python
cascade = cv2.CascadeClassifier('./haar/lbpcascade_frontalface_improved.xml')

cam = WebcamVideoStream(src=-1).start()
pre = 0

while 1:
    img = cam.read()
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)

    #fps
    cur = time.time()
    sec = cur - pre
    pre = cur

    fps = 1/sec

    cv2.putText(gray,"FPS : "+str(fps),(100,100),cv2.FONT_HERSHEY_SIMPLEX,1.5,(0,0,255),2)

    detectAndDisply(gray,cascade)

    #ESC Click -> EXIT
    if cv2.waitKey(1) & 0xFF == 27:
        m.clean()
        break

cv2.destroyAllWindows()
cam.stop()
```
## 7. motor.py

모터의 동작을 제어한다.

- 오른쪽 바퀴와 왼쪽 바퀴를 나누어서 제어
```python
def left_tire
def right_tire
```
- 동작마다 속도를 셋팅해서 제어
```python
def go
def back
def left
def right
def stop
def go_left
def go_right
def back_left
def back_right
```

## 8. 자신만의 cascade 만들기
이 홈페이지를 따라하면 쉽게 할 수 있습니다.

[https://pythonprogramming.net/haar-cascade-object-detection-python-opencv-tutorial/](https://pythonprogramming.net/haar-cascade-object-detection-python-opencv-tutorial/)

## 9. FPS 향상 시키기
- 출처 : [https://www.pyimagesearch.com/2015/12/21/increasing-webcam-fps-with-python-and-opencv/](https://www.pyimagesearch.com/2015/12/21/increasing-webcam-fps-with-python-and-opencv/)

- 정리 : [https://jjeamin.github.io/pi/2019/02/14/fps/](https://jjeamin.github.io/pi/2019/02/14/fps/)

## 10. 결과
- 속도가 느린것은 어쩔수 없는 것 같다.
	+ -> **Threading 추가후** 어느정도 속도가 나온다.
- 인식은 잘되지만 동작하는 부분을 잘 조정해야 할 것 같다.

---

# 참조
- [https://www.pyimagesearch.com/](https://www.pyimagesearch.com/)

- [https://github.com/ankitdhall/Arduino-OpenCV-Human-Follower](https://github.com/ankitdhall/Arduino-OpenCV-Human-Follower)

---

# 다른 Target
QR코드로 인식하게 하기

- [https://jjeamin.github.io/pi/2019/02/12/QR_code_reader/](https://jjeamin.github.io/pi/2019/02/12/QR_code_reader/)

객체검출 이용하기

- [https://jjeamin.github.io/opencv/2019/02/13/object_tracking/](https://jjeamin.github.io/opencv/2019/02/13/object_tracking/)
