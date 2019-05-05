# /include/darknet.h/

##network 구조체

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

- `seen` :

- `time_steps` :

- `batch` : batch size를 의미한다. batch란 한번 학습할때 값을 한개를 넣는것이 아니라 몇개의 값을 넣어서 한번에 학습시키는 것을 의미한다.

- `epoch` : 전체 학습 데이터셋을 몇번 학습시킬지에 대한 의미이다. epoch 이 1이면 전체 학습 데이터셋을 한번 학습하는 것이다.

- `subdivision` : mini batch라고도 불리는데 batch를 몇 개의 분할해서 GPU로 보내 학습하는 것이다.

- `learning_rate` : 학습률은 한번의 학습에 최적의 값에 얼마만큼 수렴시킬 것인지에 대한 학습 비율을 의미한다. (`Gradient Descent Algorithm`)

- `policy` :

- `momentum` : 모멘텀(관성) 상수를 의미하고 모멘텀은 `Gradient Descent Algorithm`의 너무 느린 단점을 보완하기 위해 사용하는 매개변수다. *개념정리*

- `decay` : 감쇠를 의미하며 가중치가 너무클수 있기 때문에 가중치를 제한해주는 매개변수다. *개념정리*

- `random` : 



## node 구조체

```
typedef struct node{
    void *val;
    struct node *next;
    struct node *prev;
} node;
```

- 이중 연결

## list 구조체

```
typedef struct list{
    int size;
    node *front;
    node *back;
} list;
```

- 이중 연결

## section 구조체

```
typedef struct{
    char *type;
    list *options;
}section;
```

# /src/option_list.h

## kvp 구조체

```
typedef struct{
    char *key;
    char *val;
    int used;
} kvp;
```

# /src/parser.c

```
typedef struct size_params{
    int batch;
    int inputs;
    int h;
    int w;
    int c;
    int index;
    int time_steps;
    network *net;
} size_params;
```
