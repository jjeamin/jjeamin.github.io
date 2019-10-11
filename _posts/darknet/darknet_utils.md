# UTILS

`/src/utils.c`

---
# cfg

## strip

```
void strip(char *s)
{
    size_t i;
    size_t len = strlen(s);
    size_t offset = 0;
    for(i = 0; i < len; ++i){
        char c = s[i];
        if(c==' '||c=='\t'||c=='\n') ++offset;
        else s[i-offset] = c;
    }
    s[len-offset] = '\0';
}
```

cfgfile에서 공백,줄바꿈,tab을 제거한다.

## basecfg

```
char *basecfg(char *cfgfile)
{
    char *c = cfgfile;
    char *next;
    while((next = strchr(c, '/')))
    {
        c = next+1;
    }
    c = copy_string(c);
    next = strchr(c, '.');
    if (next) *next = 0;
    return c;
}
```

cfg파일 이름 찾기

## fgetl
```
int feof(FILE *stream) : 파일의 끝에 도달했는지의 여부
char fgets(char *string, int n, FILE *stream) : 파일을 한줄씩 읽어들이는 함수
```

```
char *fgetl(FILE *fp)
{
    if(feof(fp)) return 0;
    size_t size = 512;
    char *line = malloc(size*sizeof(char));
    if(!fgets(line, size, fp)){ /// 파일을 한줄 읽고 파일이 끝인지 아닌지 판단
        free(line);             /// malloc 해제
        return 0;               /// return 0
    }

    size_t curr = strlen(line); /// 한줄의 문자열 길이

    while((line[curr-1] != '\n') && !feof(fp)){
        if(curr == size-1){
            size *= 2;
            line = realloc(line, size*sizeof(char));
            if(!line) {
                printf("%ld\n", size);
                malloc_error();
            }
        }
        size_t readsize = size-curr;
        if(readsize > INT_MAX) readsize = INT_MAX-1;
        fgets(&line[curr], readsize, fp);
        curr = strlen(line);
    }
    if(line[curr-1] == '\n') line[curr-1] = '\0';

    return line;
}
```

---
# random

## rand_int

```
int rand_int(int min, int max)
{
    if (max < min){
        int s = min;
        min = max;
        max = s;
    }
    int r = (rand()%(max - min + 1)) + min;
    return r;
}
```

min과 max사이에 랜덤한 값

## rand_normal

```
#define TWO_PI 6.2831853071795864769252866f

float rand_normal()
{
    static int haveSpare = 0;
    static double rand1, rand2;

    if(haveSpare)
    {
        haveSpare = 0;
        return sqrt(rand1) * sin(rand2);
    }

    haveSpare = 1;

    rand1 = rand() / ((double) RAND_MAX);             /// rand1 = 0.0 ~ 1.0
    if(rand1 < 1e-100) rand1 = 1e-100;                /// rand1 = 1e-100 ~ 1.0
    rand1 = -2 * log(rand1);                          /// rand1 = -2 * log(1e-100 ~ 1)
    rand2 = (rand() / ((double) RAND_MAX)) * TWO_PI;  /// rand2 = 0 ~ TWO_PI

    return sqrt(rand1) * cos(rand2);                  /// (0 ~ 13.xxx) * (-1 ~ 1)
}
```

- rand1



![log](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/log.PNG)



처음에 위와 같은 분포를 만들고



![sqrt](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/sqrt.PNG)



최종적으로 곱할때 루트를 취하면 위와 같은 분포가 나온다.

- rand2


![cos](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/cos.PNG)



최종적으로 곱할때 cosine을 취하면 위와 같은 분포가 나온다.

### 여기서 의문점 log라는 것은 왜사용할까?

log는 큰 수를 같은 비율의 작은 수로 바꾸어 주는 것이다. log는 큰 수를 작게 만들고 복잡한 계산을 간편하게 하기위해 사용한다. 그리고 그에 따라서 데이터 분석 시 의미있는 결과를 도출하기 위한 것이다.

## rand_uniform

```
float rand_uniform(float min, float max)
{
    if(max < min){
        float swap = min;
        min = max;
        max = swap;
    }
    return ((float)rand()/RAND_MAX * (max - min)) + min;
}
```

- `RAND_MAX` - rand 함수로 반환될 수 있는 수의 최대값

- 최소부터 최대까지 중 임의의 부동소수점(float)숫자를 리턴한다.

---

# args

## del_arg
```
void del_arg(int argc, char **argv, int index)
{
    int i;
    for(i = index; i < argc-1; ++i) argv[i] = argv[i+1];
    argv[i] = 0;
}
```

인자값 제거

## find_char_arg
```
char *find_char_arg(int argc, char **argv, char *arg, char *def)
{
    int i;
    for(i = 0; i < argc-1; ++i){
        if(!argv[i]) continue;
        if(0==strcmp(argv[i], arg)){
            def = argv[i+1];
            del_arg(argc, argv, i);
            del_arg(argc, argv, i);
            break;
        }
    }
    return def;
}
```

인자값에서 문자열을 찾는다. (하이퍼 파라미터)

## find_float_arg
```
float find_float_arg(int argc, char **argv, char *arg, float def)
{
    int i;
    for(i = 0; i < argc-1; ++i){
        if(!argv[i]) continue;
        if(0==strcmp(argv[i], arg)){
            def = atof(argv[i+1]);
            del_arg(argc, argv, i);
            del_arg(argc, argv, i);
            break;
        }
    }
    return def;
}
```

인자값에서 실수값을 찾는다. (하이퍼 파라미터)

## find_int_arg
```
int find_int_arg(int argc, char **argv, char *arg, int def)
{
    int i;
    for(i = 0; i < argc-1; ++i){
        if(!argv[i]) continue;
        if(0==strcmp(argv[i], arg)){
            def = atoi(argv[i+1]);
            del_arg(argc, argv, i);
            del_arg(argc, argv, i);
            break;
        }
    }
    return def;
}
```

인자값에서 정수값을 찾는다. (하이퍼 파라미터)

---

## copy_string

```
char *copy_string(char *s) // strncpy using function
{
    char *copy = malloc(strlen(s)+1);
    strncpy(copy, s, strlen(s)+1);
    return copy;
}
```

문자열 복사
