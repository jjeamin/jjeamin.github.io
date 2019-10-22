# network 구조체

```
typedef struct network{
    int n;                            
    int batch;                        
    size_t *seen;
    int *t;
    float epoch;                     
    int subdivisions;
    layer *layers;
    float *output;
    learning_rate_policy policy;

    float learning_rate;
    float momentum;
    float decay;
    float gamma;
    float scale;
    float power;
    int time_steps;
    int step;
    int max_batches;
    float *scales;
    int   *steps;
    int num_steps;
    int burn_in;

    int adam;
    float B1;
    float B2;
    float eps;

    int inputs;
    int outputs;
    int truths;
    int notruth;
    int h, w, c;
    int max_crop;
    int min_crop;
    float max_ratio;
    float min_ratio;
    int center;
    float angle;
    float aspect;
    float exposure;
    float saturation;
    float hue;
    int random;

    int gpu_index;
    tree *hierarchy;

    float *input;
    float *truth;
    float *delta;
    float *workspace;
    int train;
    int index;
    float *cost;
    float clip;

#ifdef GPU
    float *input_gpu;
    float *truth_gpu;
    float *delta_gpu;
    float *output_gpu;
#endif

} network;
```

*구조체 변수에 어떤 것이 있는지 간단한 코멘트와 함께 알아보겠습니다. 자세한건 후에 더 적어보겠습니다.*

- `n` : layer의 개수

- `t` :

- `seen` : 학습하면서 현재 얼만큼 진행했는지(보았는지)에 대한 정보?

- `time_steps` :

- `batch` : batch size를 의미한다. batch란 한번 학습할때 값을 한개를 넣는것이 아니라 몇개의 값을 넣어서 한번에 학습시키는 것을 의미한다.

- `epoch` : 전체 학습 데이터셋을 몇번 학습시킬지에 대한 의미이다. epoch 이 1이면 전체 학습 데이터셋을 한번 학습하는 것이다.

- `subdivision` : mini batch라고도 불리는데 batch를 몇 개의 분할해서 GPU로 보내 병렬로 학습하는 것이다. ex) batch = 64 , subdivision = 16 이라면 한번에 4장의 이미지씩 모델에 넣어서 학습돌린다.(학습돌린 후 바로 update가 아님 ) 그 후 총 64개의 이미자(4*16)가 다 완료 된 후 update 진행 (backpropagation)

즉, 몇번 나눌것이냐?

- `learning_rate` : 학습률은 한번의 학습에 최적의 값에 얼마만큼 수렴시킬 것인지에 대한 학습 비율을 의미한다. (`Gradient Descent Algorithm`)

- `momentum` : 모멘텀(관성) 상수를 의미하고 모멘텀은 `Gradient Descent Algorithm`의 너무 느린 단점을 보완하기 위해 사용하는 매개변수다. *개념정리*

- `decay` : 감쇠를 의미하며 가중치가 너무 클수 있기 때문에 가중치를 제한해주는 매개변수다. *개념정리*

- `random` : random이 1일 경우 10회마다 네트워크의 크기(입력,출력)를 조정한다.

*Yolo는 종횡비를 유지합니다. 즉, 이미지 1280x720은 416x234 크기로 조정 된 다음 416x416 네트워크에 삽입됩니다.*

- `adam` : adam 사용의 유무를 저장해주는 변수(0,1) *개념정리*
  + `B1` : momentum을 위한 decay 비율
  + `B2` : momentum을 위한 decay 비율
  + `eps` : `엡실론` 임의의 작은 양수

- `h` : image의 높이

- `w` : image의 폭

- `c` : image의 channel 수

- `max_crop` : 최대 crop image의 크기 w*2

- `min_crop` : 최소 crop image의 크기 w

- `max_ratio` :

- `min_ratio` :

- `center` :

- `clip` :

- `angle` : 이 각도 만큼 회전해서 image augment

- `aspect`

- `saturation`

- `exposure`

- `hue`

- `burn_in` : 첫번째 batch의 경우 learning rate를 최종값(learning_rate parameter value) 까지 천천히 증가 시킨다. 이 값을 사용해 loss값이 감소하기 전에 monitering을 통해서 learning rate의 값을 결정한다.

- `max_batches` : 최대 반복 횟수

- `power` : learning rate decay 파라미터

- `policy` : learning rate decay policy
  + `step` : 몇번의 배치 후에 learning rate를 바꿀 것인가 (500)
  + `scale` : step이 끝나고 scale을 곱해서 learning rate 조정 (0.1) -> 500번째*0.1
  + `steps` : 몇번의 배치 후에 learning rate를 바꿀 것인가 (500,1000)
  + `scales` : steps이 끝나고 scales을 곱해서 learning rate 조정 (0.1,0.2) -> 500*0.1*1000*0.2
  + `gamma` : learning rate decay 파라미터
