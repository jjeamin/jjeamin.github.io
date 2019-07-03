---
layout: post
title:  "QR CODE 읽기"
summary: "opencv와 zbar를 이용해 QR CODE 인식하기 using Raspberry"
date:   2019-02-12 13:10 -0400
categories: pi
---

[[깃허브](https://github.com/jjeamin/Raspi_QRread)]

# 구성
- Raspberry PI 3(라즈비안)
- PI camera

# requirement
- python 2.7
- opencv 4.0.0
- zbar
- pyzbar

## 패키지 설치
zbar

```
sudo apt-get install libzbar0
```

pyzbar

```
pip install pyzbar
```

---
# CODE

## qr.py

```python
import cv2
from pyzbar import pyzbar

cam = cv2.VideoCapture(-1)

while(True):
        ret, frame = cam.read()
        barcodes = pyzbar.decode(frame)

        for barcode in barcodes:
                (x,y,w,h) = barcode.rect
                cv2.rectangle(frame,(x,y),(x+w,y+h),(0,0,255),2)

        cv2.imshow('image',frame)

        k = cv2.waitKey(10) & 0xFF
        if k == 27:
                break

cam.release()
cv2.destroyAllWindows()

```

단순하게 QR code를 읽는 코드이다.

---

## 기존 [following_robot](https://jjeamin.github.io/pi/2019/02/07/following_robot/) 코드 변경

## 기존 소스

``` python
def detectAndDisply(img,cascade):
    detector = cascade.detectMultiScale(img)

    max_size = -1
    index = 0

    if(len(detector) != 0):
        for (x,y,w,h) in detector:
            if w*h > max_size:
                max_size = w*h
                max_pos = index
            index += 1

        max_rect = detector[max_pos]
        (max_x,max_y,max_w,max_h) = detector[max_pos]

        cv2.rectangle(img,(max_x,max_y),(max_x + max_w,max_y + max_h),(0,255,0),2)

        move(detector[max_pos])

    cv2.imshow('img',img)

```

## 변경 소스

``` python
def detectAndDisply(img):
    max_size = -1
    index = 0
    barcodes = pyzbar.decode(img)

    if(len(barcodes) != 0):
        for barcode in barcodes:
            (x,y,w,h) = barcode.rect
            cv2.rectangle(img,(x,y),(x+w,y+h),(0,255,0),2)
        move((x,y,w,h))
    else:
        m.stop()

    cv2.imshow('img',img)
```

---

# 결과
- 얼굴인식보다 속도가 매우 빠르다.
- 간단한 코드

---

# 참조
- [https://www.pyimagesearch.com/2018/05/21/an-opencv-barcode-and-qr-code-scanner-with-zbar/](https://www.pyimagesearch.com/2018/05/21/an-opencv-barcode-and-qr-code-scanner-with-zbar/)
- [https://www.learnopencv.com/barcode-and-qr-code-scanner-using-zbar-and-opencv/](https://www.learnopencv.com/barcode-and-qr-code-scanner-using-zbar-and-opencv/)
