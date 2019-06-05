---
layout: post
title:  "Face Identification"
summary: "Face_recognition을 이용한 얼굴 인식 using Raspberry"
date:   2019-02-12 09:10 -0400
categories: pi
---
# 얼굴검출 방식
1. 얼굴을 찾습니다.
2. 고유한 특징을 찾습니다.
3. 학습시킵니다.
4. 누구인지 알아냅니다.

---

# Dependency
- Raspberry PI 3(라즈비안)
- PI camera

# requirement
- python 2.7
- opencv 4.0.0 [[<span style="color:blue">다운로드 방법</span>]](https://webnautes.tistory.com/916)
- Face_recognition(deep learning)

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

## 1. 패키지 설치(공식적)
라즈베리파이는 메모리가 1GB 밖에 되지않기 때문에 파이썬 라이브러리를 설치하기가 곤란합니다.

```
- Face_recognition
  + dlib
    + numpy
    + scipy
    + scikit-image
```

따라서 아래 홈페이지를 잘 따라서 설치하시기를 바랍니다. (좀 오래 걸립니다..)
- dlib 설치
  + [https://www.pyimagesearch.com/2017/05/01/install-dlib-raspberry-pi/](https://www.pyimagesearch.com/2017/05/01/install-dlib-raspberry-pi/)

- Face_recognition 설치
  + [https://gist.github.com/ageitgey/1ac8dbe8572f3f533df6269dab35df65](https://gist.github.com/ageitgey/1ac8dbe8572f3f533df6269dab35df65)

약 라이브러리 설치도중 오류가 발생한다면 오류 마지막 부분을 잘 읽어본 뒤에 찾으시길 바랍니다. (삽질은 끝이없다...)

---

## 2. 패키지 설치(직접 한 것)

*opencv 설치가 **끝난 상태**로 진행했습니다*

### 설치전 초기설정

```
$ sudo vi /etc/dphys-swapfile
```

CONFIG_SWAPSIZE : 100 -> 1024

```
$ sudo /etc/init.d/dphys-swapfile stop
$ sudo /etc/init.d/dphys-swapfile start
```

부팅모드 변경하기

```
$ sudo raspi-config
```

- Boot Options => Desktop / CLI => Console Autologin
- Advanced Options => Memory Split : 16으로 설정

저장 후

```
$ reboot
```

부팅모드로 진입했다면

### 툴설치

```
$ sudo apt-get update
$ sudo apt-get install build-essential cmake
$ sudo apt-get install libgtk-3-dev
$ sudo apt-get install libboost-all-dev
```

### pip 설치

```
$ wget https://bootstrap.pypa.io/get-pip.py
$ sudo python get-pip.py
```

### 패키지 설치

```
$ pip install numpy
$ pip install scipy
$ pip install cython
$ pip install scikit-image
$ pip install dlib
$ pip --no-cache-dir install face_recognition
```

여기까지 하는데 하루의 반은 쓴거 같습니다.

---

## 3. face_recognition
- [https://github.com/ageitgey/face_recognition](https://github.com/ageitgey/face_recognition)
- dlib(deeplearning)


## 4. 테스트
출처 : [깃허브](https://github.com/ageitgey/face_recognition/blob/master/examples/facerec_from_webcam_faster.py)

```python
import face_recognition
import cv2

# This is a demo of running face recognition on live video from your webcam. It's a little more complicated than the
# other example, but it includes some basic performance tweaks to make things run a lot faster:
#   1. Process each video frame at 1/4 resolution (though still display it at full resolution)
#   2. Only detect faces in every other frame of video.

# PLEASE NOTE: This example requires OpenCV (the `cv2` library) to be installed only to read from your webcam.
# OpenCV is *not* required to use the face_recognition library. It's only required if you want to run this
# specific demo. If you have trouble installing it, try any of the other demos that don't require it instead.

# Get a reference to webcam #0 (the default one)
video_capture = cv2.VideoCapture(-1)

# Load a sample picture and learn how to recognize it.
obama_image = face_recognition.load_image_file("obama.jpg")
obama_face_encoding = face_recognition.face_encodings(obama_image)[0]

# Load a second sample picture and learn how to recognize it.
biden_image = face_recognition.load_image_file("biden.jpg")
biden_face_encoding = face_recognition.face_encodings(biden_image)[0]

# Create arrays of known face encodings and their names
known_face_encodings = [
    obama_face_encoding,
    biden_face_encoding
]
known_face_names = [
    "Barack Obama",
    "Joe Biden"
]

# Initialize some variables
face_locations = []
face_encodings = []
face_names = []
process_this_frame = True

while True:
    # Grab a single frame of video
    ret, frame = video_capture.read()

    # Resize frame of video to 1/4 size for faster face recognition processing
    small_frame = cv2.resize(frame, (0, 0), fx=0.25, fy=0.25)

    # Convert the image from BGR color (which OpenCV uses) to RGB color (which face_recognition uses)
    rgb_small_frame = small_frame[:, :, ::-1]

    # Only process every other frame of video to save time
    if process_this_frame:
        # Find all the faces and face encodings in the current frame of video
        face_locations = face_recognition.face_locations(rgb_small_frame)
        face_encodings = face_recognition.face_encodings(rgb_small_frame, face_locations)

        face_names = []
        for face_encoding in face_encodings:
            # See if the face is a match for the known face(s)
            matches = face_recognition.compare_faces(known_face_encodings, face_encoding)
            name = "Unknown"

            # If a match was found in known_face_encodings, just use the first one.
            if True in matches:
                first_match_index = matches.index(True)
                name = known_face_names[first_match_index]

            face_names.append(name)

    process_this_frame = not process_this_frame


    # Display the results
    for (top, right, bottom, left), name in zip(face_locations, face_names):
        # Scale back up face locations since the frame we detected in was scaled to 1/4 size
        top *= 4
        right *= 4
        bottom *= 4
        left *= 4

        # Draw a box around the face
        cv2.rectangle(frame, (left, top), (right, bottom), (0, 0, 255), 2)

        # Draw a label with a name below the face
        cv2.rectangle(frame, (left, bottom - 35), (right, bottom), (0, 0, 255), cv2.FILLED)
        font = cv2.FONT_HERSHEY_DUPLEX
        cv2.putText(frame, name, (left + 6, bottom - 6), font, 1.0, (255, 255, 255), 1)

    # Display the resulting image
    cv2.imshow('Video', frame)

    # Hit 'q' on the keyboard to quit!
    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

# Release handle to the webcam
video_capture.release()
cv2.destroyAllWindows()
```

이미지를 한장만 가지고 학습이 잘되서 좋은거 같습니다. 테스트를 할 때는 자신의 이미지를 사용해야합니다.

# 결과
- 실행시간이 조금 걸립니다.
- 인식 후 초당 1프레임 정도 나옵니다.


![obama](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/faceid/obama.JPG)



---

# 참조
- [https://www.pyimagesearch.com/2018/06/25/raspberry-pi-face-recognition/](https://www.pyimagesearch.com/2018/06/25/raspberry-pi-face-recognition/)
- [https://github.com/jjeamin/faceid](https://github.com/jjeamin/faceid)
- [https://gist.github.com/ageitgey/1ac8dbe8572f3f533df6269dab35df65](https://gist.github.com/ageitgey/1ac8dbe8572f3f533df6269dab35df65)
- [https://www.pyimagesearch.com/2017/05/01/install-dlib-raspberry-pi/](https://www.pyimagesearch.com/2017/05/01/install-dlib-raspberry-pi/)
