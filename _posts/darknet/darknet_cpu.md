# CPU 계산부분

`/src/bias.c`

## fill_cpu

```
void fill_cpu(int N, float ALPHA, float *X, int INCX)
{
    int i;
    for(i = 0; i < N; ++i) X[i*INCX] = ALPHA;
}
```

X를 0 ~ N까지 ALPHA 값으로 초기화

---

`/src/im2col.c`

## im2col_cpu

```
void im2col_cpu(float* data_im, int channels,  int height,  int width, int ksize,  int stride, int pad, float* data_col)
{
    int c,h,w;
    int height_col = (height + 2*pad - ksize) / stride + 1;     /// output 크기
    int width_col = (width + 2*pad - ksize) / stride + 1;

    int channels_col = channels * ksize * ksize;                ///
    for (c = 0; c < channels_col; ++c) {
        int w_offset = c % ksize;
        int h_offset = (c / ksize) % ksize;
        int c_im = c / ksize / ksize;
        for (h = 0; h < height_col; ++h) {
            for (w = 0; w < width_col; ++w) {
                int im_row = h_offset + h * stride;
                int im_col = w_offset + w * stride;
                int col_index = (c * height_col + h) * width_col + w;
                data_col[col_index] = im2col_get_pixel(data_im, height, width, channels,
                        im_row, im_col, c_im, pad);
            }
        }
    }
}

```

이미지를 columns(열) 로 바꾸어주는 함수이다. 즉, 연산을 빠르게 하기 위해서 다차원 배열을 2차원 행렬 연산으로 바꾸어주는 것을 의미한다. 아래 그림과 같이 표현할 수 있다.



![im2col](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/im2col.PNG)



자세한 계산은 아래와 같다.



![im2col2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/im2col2.PNG)



## im2col_get_pixel

```
float im2col_get_pixel(float *im, int height, int width, int channels,
                        int row, int col, int channel, int pad)
{
    row -= pad;
    col -= pad;

    if (row < 0 || col < 0 ||
        row >= height || col >= width) return 0;
    return im[col + width*(row + height*channel)];
}
```

image에서 pixel을 찾아주는 역할
