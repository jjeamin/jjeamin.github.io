# DATA

`/src/data.c`

---

# Thread

## load_data_in_thread

```
pthread_t load_data_in_thread(load_args args) // third function when you use pthread in detector.c train_detector() function
{
    pthread_t thread;
    struct load_args *ptr = calloc(1, sizeof(struct load_args));
    *ptr = args;
    if(pthread_create(&thread, 0, load_thread, ptr)) error("Thread creation failed"); // call load_thread() function
    return thread;
}
```

# get

## get_next_batch

```
void get_next_batch(data d, int n, int offset, float *X, float *y)
{
    int j;
    for(j = 0; j < n; ++j){
        int index = offset + j;
        memcpy(X+j*d.X.cols, d.X.vals[index], d.X.cols*sizeof(float)); // 학습 이미지 batch 만큼 불러오기
        if(y) memcpy(y+j*d.y.cols, d.y.vals[index], d.y.cols*sizeof(float)); // 정답 데이터 batch 만큼 불러오기
    }
}
```
