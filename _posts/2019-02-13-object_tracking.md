---
layout: post
title:  "Object Tracking"
summary: "객체 추적"
date:   2019-02-13 10:10 -0400
categories: opencv
---
# requirement
- OpenCV

# HSV
- H(Hue) : 색조
- S(Saturation) : 채도
- V(Value) : 명도

```python
hsv = cv2.cvtColor(frame,cv2.COLOR_BGR2HSV)
```

색을 표현하는 방법이며 RGB는 빨강,파랑,초록의 조합으로 표현하지만, HSV는 우리가 보는 그대로의 색을 Hue 채널로 나타낸다. 조합이 아닌 있는 그대로 보여준다.



![hsv](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/object/hsv.JPG)



## cv2.inRange()

```python
mask = cv2.inRange(img,color_lower,color_upper)
```

lower ~ upper 영역의 색을 남긴다.

## cv2.erode()

```python
mask = cv2.erode(img,kernel,iterations=1)
```

- img : 이미지
- kernel : 커널
- iterations : 반복 횟수

이미지의 경계부분을 침식시켜서 배경 이미지로 전환합니다. 즉, 이미지가 반복횟수를 늘릴수록 가늘어지게 됩니다.

## cv2.dilate()

```
mask = cv2.dilate(img,kernel,iterations=1)
```

- img : 이미지
- kernel : 커널
- iterations : 반복 횟수

이미지의 경계부분을 확장시켜서 배경 이미지로 전환합니다. 즉, 이미지가 반복횟수를 늘릴수록 두꺼워지게 됩니다.

---

# Image Contour
- 컨투어란 동일한 픽셀값을 가지고 있는 영역의 경계선입니다.
- 윤곽선,외형을 파악하는데 사용됩니다.

## cv2.findcontours()

```python
cnts = cv2.findcontours(img,option,method)
```

이미지안에 모든 컨투어 영역을 찾아주는 함수입니다.

- img : 이미지

- option : 컨투어를 찾는 방법
  + cv2.RETR_EXTERNAL	: 가장 바깥쪽 라인

- method : 컨투어를 표현하는 방법
  + cv2.CHAIN_APPROX_SIMPLE : 라인을 그릴수 있는 포인트만 반환

## cv2.boundingRect()

```python
x,y,w,h = cv2.boundingRect(cnt)
cv2.rectangle(frame,(x,y),(x+w,y+h),(0, 0, 255))
```

컨투어 영역의 사각형 좌표를 찾아줍니다. 사각형 영역까지 그려주었습니다.

## cv2.minEnclosingCircle()

```python
(x,y),radius = cv2.minEnclosingCircle(cnt)
center = (int(x),int(y))
radius = int(radius)
cv2.circle(frame,center,radius,7)
```

컨투어 영역의 원형 좌표를 찾아줍니다. 원형 영역까지 그려주었습니다.



![circle](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/object/circle.JPG)



---

# Tracking
추적하는 경로를 시각화하려면 매번 검출된 영역의 중심 위치의 변화값을 잘 저장하고 잘 처리해야합니다. 그렇기 때문에 자료구조 **deque** 를 사용합니다.

## deque
deque는 양방향 queue 입니다. 앞이나 뒤에서 값을 추가하거나 값을 출력할수 있습니다.

**선언**

```python
from collections import deque

track = deque(maxlen=64)
```

**추적선 그리기**

```python
#tracking
	track.appendleft(center)

	for i in range(1,len(track)):
		if track[i-1] is None or track[i] is None:
			continue

		thickness = int(np.sqrt(64/float(i+1))*2.5)
		cv2.line(frame,track[i-1],track[i],(0,0,255),thickness)

  cv2.imshow('test',frame)
```



![tracking](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/object/tracking.JPG)



# 참조
- [https://datascienceschool.net/view-notebook/f9f8983941254a34bf0fee42c66c5539/](https://datascienceschool.net/view-notebook/f9f8983941254a34bf0fee42c66c5539/)

- [https://www.pyimagesearch.com/2015/09/14/ball-tracking-with-opencv/](https://www.pyimagesearch.com/2015/09/14/ball-tracking-with-opencv/)

- [https://m.blog.naver.com/samsjang/220505815055](https://m.blog.naver.com/samsjang/220505815055)
