# convolution backpropagation

## backward_convolutional_layer

```
void backward_convolutional_layer(convolutional_layer l, network net)
{
    int i, j;
    int m = l.n/l.groups;                                                           /// filter 개수
    int k = l.size*l.size*l.c/l.groups;                                             /// filter 크기
    int n = l.out_w*l.out_h;                                                        /// output 크기

    gradient_array(l.output, l.outputs*l.batch, l.activation, l.delta);             /// activation function 미분값 적용
                                                                                    /// delta에 값이 있다.
    if(l.batch_normalize){
        backward_batchnorm_layer(l, net);                                           /// batch normalize 미분값 적용
    } else {
        backward_bias(l.bias_updates, l.delta, l.batch, l.n, k);                    /// bias update할 값 업데이트
    }

    for(i = 0; i < l.batch; ++i){
        for(j = 0; j < l.groups; ++j){
            float *a = l.delta + (i*l.groups + j)*m*k;                              /// gradient 포인터 이동
            float *b = net.workspace;                                               /// 작업공간
            float *c = l.weight_updates + j*l.nweights/l.groups;                    /// weight update할 값 포인터 이동

            float *im  = net.input + (i*l.groups + j)*l.c/l.groups*l.h*l.w;         /// 이미지 포인터
            float *imd = net.delta + (i*l.groups + j)*l.c/l.groups*l.h*l.w;         /// 이미지 작업공간

            if(l.size == 1){                                                        /// filter 사이즈 1
                b = im;                                                             
            } else {
                im2col_cpu(im, l.c/l.groups, l.h, l.w,
                        l.size, l.stride, l.pad, b);                                /// image를 2차원 matrix로 변환
            }

            gemm(0,1,m,n,k,1,a,k,b,k,1,c,n);                                        /// b(image)를 전치행렬로 계산

            if (net.delta) {
                a = l.weights + j*l.nweights/l.groups;                              /// weight 포인터 이동          
                b = l.delta + (i*l.groups + j)*m*k;                                 /// gradient 포인터 이동
                c = net.workspace;                                                  /// 작업공간
                if (l.size == 1) {
                    c = imd;
                }

                gemm(1,0,n,k,m,1,a,n,b,k,0,c,k);                                    /// a(weight)를 전치행렬로 계산

                if (l.size != 1) {
                    col2im_cpu(net.workspace, l.c/l.groups, l.h, l.w, l.size, l.stride, l.pad, imd);    /// columns -> image로 변환
                }
            }
        }
    }
}
```

역전파를 진행한다.

## backward_bias

```
void backward_bias(float *bias_updates, float *delta, int batch, int n, int size)
{
    int i,b;
    for(b = 0; b < batch; ++b){
        for(i = 0; i < n; ++i){
            bias_updates[i] += sum_array(delta+size*(i+b*n), size);
        }
    }
}
```

gradient를 갖고 있는 delta를 이용해 bias를 업데이트 할 값을 구한다.

## sum_array

```
float sum_array(float *a, int n)
{
    int i;
    float sum = 0;
    for(i = 0; i < n; ++i) sum += a[i];
    return sum;
}
```

---

## update_convolutional_layer

```
void update_convolutional_layer(convolutional_layer l, update_args a)
{
    float learning_rate = a.learning_rate*l.learning_rate_scale;
    float momentum = a.momentum;
    float decay = a.decay;
    int batch = a.batch;

    axpy_cpu(l.n, learning_rate/batch, l.bias_updates, 1, l.biases, 1);
    scal_cpu(l.n, momentum, l.bias_updates, 1);

    if(l.scales){
        axpy_cpu(l.n, learning_rate/batch, l.scale_updates, 1, l.scales, 1);
        scal_cpu(l.n, momentum, l.scale_updates, 1);
    }

    axpy_cpu(l.nweights, -decay*batch, l.weights, 1, l.weight_updates, 1);
    axpy_cpu(l.nweights, learning_rate/batch, l.weight_updates, 1, l.weights, 1);
    scal_cpu(l.nweights, momentum, l.weight_updates, 1);
}
```
