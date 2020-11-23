---
layout: post
title:  "가속기 프로그래밍 겨울학교"
summary: "2019 가속기 프로그래밍 겨울학교"
date:   2019-02-18 09:00 -0400
categories: GPU
math: true
---

# 병렬처리의 기초 1

## CPU 전력장벽
- **전력소모** : core clock frequency에 비례
- **열 발산** : 전력소모에 비례

### ILP(instruction Level Parallelism)
- 프로세서의 성능을 높이는 또 다른 방법
- 장벽 : 동시에 실행될 수 있는 인스트럭션이 많지 않다!!

## 멀티코어
- 두 개 이상의 독립적인 프로세서를 담고 있는 칩
- 전력장벽과 ILP장벽의 해결책

## Accelerator
- FPGA
- GPU

## 동종(Homogeneous) 멀티코어
여러 개의 같은 종류의 코어가 하나의 칩 안에 존재

## 이종(Heterogeneous) 컴퓨터 시스템
두 가지 이상의 서로 다른 종류의 프로세서를 가진 컴퓨터 시스템
- CPU,GPU,FPGA,ASIC,DSP
- 고성능 달성이 쉽고 전력효율이 좋다.
- 범용 프로세서(자원 관리)(CPU) + 가속기(계산전용)(GPU)

## Amdahl의 법칙
- p : 병렬화 될 수 있는 부분의 순차실행 시간
- 1-p : 병렬화 될 수 없는 부분의 순차실행 시간
- n : 프로세서의 개수

$$
1 \over (1-p)+ {p \over n}
$$

## 병렬프로그래밍
- 고성능과 쉬운 프로그래밍을 동시에 달성하기 매우 어렵다.
- OpenCL : 오픈되어 있다.
- CUDA : NVIDIA 독점
- OpenCL과 CUDA는 Low level이고 사용을 많이 하므로 믿고 배울 수 있다.

## 폰노이만 아키텍처
- 입력장치
- 출력장치
- 주기억 장치 : 메모리
- 중앙 처리 장치 : CPU

## 머신코드
- 읽기, 해석, 실행하는 프로그램
  + Fetch
  + Decode
  + Execute
- CPU가 실행

## 파이프라이닝(Pipelining)

- 하드웨어 테크닉으로 인스트럭션을 처리하는 throughput(처리량)을 증가시키는방법

#### In-order 실행

- 실행 부분을 분류 : 정수,부동소수점,로드/스토어

#### Out-of-Order 실행

- In-order
- reservation station : 실행전 대기
- **reorder** buffer : 순서를 재배열

#### Superscalar CPU

- Out-of-Order
- **ILP** 를 이용하여 성능을 높임


## 병렬성
- ILP
- 태스크 병렬성
- 데이터 병렬성 - **중요**

#### 태스크 병렬성
- 한명당 요리 하나씩 맡아서 하기

#### 데이터 병렬성
- 4가지 요리를 각자 조금씩 다하기 : 많은 준비가 가능하다.
- **루프 레벨 병렬성**
- 데이터가 많을수록 병렬성도 증가

#### SPMD
- Single Program, Multiple Data
- OpenCL,CUDA

---

# 천둥
### 작업 스케줄러

```
thor
```

### 작업추가

```
thorq --add --mode single ./exec 10
```

### 작업 상태 확인

```
thorq --stat 400000
```

### 실행 결과 확인

```
cat task_400000.stdout
```

### GPU 사용하기

```
thorq --add --mode single --device gpu/7970 ./exec
```

### timeout 설정

```
thorq --add --mode single --timeout 100 ./exec
```

### 기타

```
thorq --kill-all
thorq --stat-all
```

### plot_input.py

```
./plot_input.py <입력파일> <new.png>
```

### plot_output.py

```
./plot_output.py <입력파일> <출력파일> <new.png>
```

### gen_input.py

```
./gen_input.py <점개수> <클러스터개수> <생성할입력파일>
```
