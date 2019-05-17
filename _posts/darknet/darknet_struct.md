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
