# STRUCT

`/include/darknet.h`

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

## matrix 구조체

```
typedef struct matrix{
    int rows, cols;
    float **vals;
} matrix;
```

- 행렬

## box 구조체

```
typedef struct{
    float x, y, w, h;
} box;
```

- box offset

## data 구조체

```
typedef struct{
    int w, h;
    matrix X; // X.cols = total pixel , rows = batch(64) , vals = each pixel's value (we can find information in data.c's load_data_detection)
    matrix y; // y.cols = 5*boxes( boxes default 90 ) , y.rows = batch(64) ,
    int shallow;
    int *num_boxes;
    box **boxes;
} data;
```

X : cols, rows
- cols : 이미지 한장의 픽셀
- rows : 이미지의 개수 batch size

Y : cols, rows
- cols : 이미지 한장에서의 box의 개수
- rows : 이미지의 개수 batch size

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

## size_params 구조체

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

- input image에 대한 정보
