# /src/bias.c

## fill_cpu

```
void fill_cpu(int N, float ALPHA, float *X, int INCX)
{
    int i;
    for(i = 0; i < N; ++i) X[i*INCX] = ALPHA;
}
```

X를 0 ~ N까지 ALPHA 값으로 초기화
