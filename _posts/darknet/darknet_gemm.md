# GAMM

이 단어는 딥러닝의 핵심이라고 한다. 딥러닝을 하면서 이런 단어는 처음 들어봤는데 이번에 본격적으로 알아봐야겠다.

- [여기](https://petewarden.com/2015/04/20/why-gemm-is-at-the-heart-of-deep-learning/)를 해석해보면서 개념을 자세히 알아보아야겠다.

이 분은 딥러닝이 어떻게 더 빠르게 배우고 더 많은 전력을 효율적으로 사용하는지에 대해 생각하는 사람이라고 한다... 멋지다고 생각합니다. 그래서 GEMM이라는 함수에 초점을 맞춘다고 한다. 이건 1979년에 만들어진 BLAS(Basic Linear Algebra Subprograms) 라이브러리의 일부이다. `GEMM`은 `GEneral Matrix to Matrix`의 약자이다. **두개의 입력 행렬을 곱해서 출력을 얻는 것이다**. 이 연산과 3D 그래픽에서 사용된 행렬 연산의 차이점은 행렬이 종종 매우 크다는 것이다.

## Fully Connected Layers
FC layer는 입력의 모든 값을 보고 해당 입력에 대해 해당 가중치를 모두 곱한 다음 그 결과를 합산해서 결과를 얻는다.



![gamm_fc](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/gamm_fc.PNG)



`M`은 입력 값의 개수고 `K`라는 입력 값이 있고 `N`뉴런이 있다. 각 뉴런에는 학습된 가중치값이 있고 내적을 계산한다.

## Convolutional Layers
Convolution layer에 GEMM을 선택하는 것이 좋다. 높이,폭,깊이가 있는 이미지를 2차원 이미지로 처리해야한다. convolution 연산은 커널을 사용해서 출력을 생성하기 때문에 이미지 전체에 적용 할 수 있다. 각 커널은 3차원 배열이고 깊이는 입력 이미지와 동일하지만 폭과 높이가 훨씬 작다. 적용되는 각 지점에서 해당 입력 값과 가중치가 모두 곱해지고 합쳐져서 출력이 된다. 이 것은 `edge detector`와 같이 생각할 수 있다. 커널은 가중치 패턴을 포함하고 있고 입력 이미지의 한 부분이 비슷한 패턴을 가지고 있으면 높은 값을 출력한다.

## GEMM Convolution Layers
첫번째 단계는 3차원 배열인 이미지에서 입력을 행렬로 처리할 수 있는 2차원 배열로 변환하는 것이다. 즉, im2col이라고 생각하면 된다. 만약 stride가 커널의 크기보다 작으면 이 변환을 실행할 때 발생하는 메모리 크기 확장에 두려워할 것이다. 이 뜻은 중복된 커널에 포함된 픽셀은 행렬에 복제되므로 비효율적이다. `이러한 낭비가 장점보다 중요하다.` 2차원 배열로 변환 후 계산을 한뒤 출력값을 다시 3차원 배열로 변경해야한다.

## 왜 Convolution에서 GEMM을 사용하나??
왜 그렇게 하는지는 아직 명확하지 않다. 간단하게 말하면 행렬 곱셉을 수행하기 위해 최적화를 많이 했고 일반적으로 매우 규칙적인 메모리 접근 패턴의 이점은 낭비되는 스토리지 비용보다 중요하다. GEMM의 주된 경쟁자는 푸리에 변환을 이용해 주파수 공간에서 작업을 수행하는 것이지만 효율성이 떨어진다고 한다.

# 참조
- [https://petewarden.com/2015/04/20/why-gemm-is-at-the-heart-of-deep-learning/](https://petewarden.com/2015/04/20/why-gemm-is-at-the-heart-of-deep-learning/)

---

`/src/gemm.c/`

## gemm

```
/**
TA , TB : A와 B를 곱하기전에 transpose(전치) 연산을 A에 적용할 것인지 B에 적용할 것인지 여부
M : filter 개수
N : output 크기
K : filter 크기
ALPHA :
A : weights 포인터
lda : output 크기(포인터 이동용)
B : input_columns 포인터
ldb : filter 크기(포인터 이동용)
BETA : 초기화 값
C : outputs 포인터
ldc : filter 크기(포인터 이동용)

**/
void gemm(int TA, int TB, int M, int N, int K, float ALPHA,
        float *A, int lda,
        float *B, int ldb,
        float BETA,
        float *C, int ldc)
{
    gemm_cpu( TA,  TB,  M, N, K, ALPHA,A,lda, B, ldb,BETA,C,ldc);
}
```

## gemm_cpu

```
void gemm_cpu(int TA, int TB, int M, int N, int K, float ALPHA,
        float *A, int lda,
        float *B, int ldb,
        float BETA,
        float *C, int ldc)
{
    //printf("cpu: %d %d %d %d %d %f %d %d %f %d\n",TA, TB, M, N, K, ALPHA, lda, ldb, BETA, ldc);
    int i, j;
    for(i = 0; i < M; ++i){
        for(j = 0; j < N; ++j){
            C[i*ldc + j] *= BETA;                             /// outputs 초기화
        }
    }
    if(!TA && !TB)
        gemm_nn(M, N, K, ALPHA,A,lda, B, ldb,C,ldc);
    else if(TA && !TB)
        gemm_tn(M, N, K, ALPHA,A,lda, B, ldb,C,ldc);
    else if(!TA && TB)
        gemm_nt(M, N, K, ALPHA,A,lda, B, ldb,C,ldc);
    else
        gemm_tt(M, N, K, ALPHA,A,lda, B, ldb,C,ldc);
}
```

gemm 연산을 실행시키는 부분

## gemm_nn

```
void gemm_nn(int M, int N, int K, float ALPHA,
        float *A, int lda,
        float *B, int ldb,
        float *C, int ldc)
{
    int i,j,k;
    #pragma omp parallel for
    for(i = 0; i < M; ++i){
        for(k = 0; k < K; ++k){
            register float A_PART = ALPHA*A[i*lda+k];
            for(j = 0; j < N; ++j){
                C[i*ldc+j] += A_PART*B[k*ldb+j];
            }
        }
    }
}
```

전치행렬을 사용하지 않고 연산

## gemm_nt

```

void gemm_nt(int M, int N, int K, float ALPHA,
        float *A, int lda,
        float *B, int ldb,
        float *C, int ldc)
{
    int i,j,k;
    #pragma omp parallel for
    for(i = 0; i < M; ++i){
        for(j = 0; j < N; ++j){
            register float sum = 0;
            for(k = 0; k < K; ++k){
                sum += ALPHA*A[i*lda+k]*B[j*ldb + k];
            }
            C[i*ldc+j] += sum;
        }
    }
}
```

weight를 전치행렬

## gemm_tn

```
void gemm_tn(int M, int N, int K, float ALPHA,
        float *A, int lda,
        float *B, int ldb,
        float *C, int ldc)
{
    int i,j,k;
    #pragma omp parallel for
    for(i = 0; i < M; ++i){
        for(k = 0; k < K; ++k){
            register float A_PART = ALPHA*A[k*lda+i];
            for(j = 0; j < N; ++j){
                C[i*ldc+j] += A_PART*B[k*ldb+j];
            }
        }
    }
}
```

input을 전치행렬

## gemm_tt

```
void gemm_tt(int M, int N, int K, float ALPHA,
        float *A, int lda,
        float *B, int ldb,
        float *C, int ldc)
{
    int i,j,k;
    #pragma omp parallel for
    for(i = 0; i < M; ++i){
        for(j = 0; j < N; ++j){
            register float sum = 0;
            for(k = 0; k < K; ++k){
                sum += ALPHA*A[i+k*lda]*B[k+j*ldb];
            }
            C[i*ldc+j] += sum;
        }
    }
}
```

input,weight 둘다 전치행렬
