## 한글설정

```
sudo raspi-config
```

1. local 설정하기
- en-US UTF 8
- en-GB UTF 8
- ko-KR UTF 8

2. 키보드 layout
- Other -> 한국 -> 104/105


3. 한글 깨짐 해결
```
sudo apt-get install ibus ibus-hangul fonts-unfonts-core
```


## 화면 캡쳐

```
$ scrot
```

- [http://www.rasplay.org/?p=3786](http://www.rasplay.org/?p=3786)
