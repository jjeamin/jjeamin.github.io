# /include/darknet.h/

##network 구조체

```
typedef struct network{
    int n;                            /// layer 개수
    int batch;                        /// 배치
    size_t *seen;
    int *t;
    float epoch;                      /// epoch
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
