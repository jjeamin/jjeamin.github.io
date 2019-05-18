# convolution propagation

## forward_convolutional_layer

```
void forward_convolutional_layer(convolutional_layer l, network net)
{
    int i, j;

    fill_cpu(l.outputs*l.batch, 0, l.output, 1);                          /// output을 0으로 초기화

    if(l.xnor){                                                           /// xnor 사용시
        binarize_weights(l.weights, l.n, l.c/l.groups*l.size*l.size, l.binary_weights);
        swap_binary(&l);
        binarize_cpu(net.input, l.c*l.h*l.w*l.batch, l.binary_input);
        net.input = l.binary_input;
    }

    int m = l.n/l.groups;                                                           /// filter 개수
    int k = l.size*l.size*l.c/l.groups;                                             /// filter 크기
    int n = l.out_w*l.out_h;                                                        /// output 크기
    for(i = 0; i < l.batch; ++i){
        for(j = 0; j < l.groups; ++j){
            float *a = l.weights + j*l.nweights/l.groups;                           /// 배열에서 학습 시작 포인터 이동
            float *b = net.workspace;                                               /// workspace 작업공간?
            float *c = l.output + (i*l.groups + j)*n*m;                             /// output 위치 이동
            float *im =  net.input + (i*l.groups + j)*l.c/l.groups*l.h*l.w;         /// 배열에서 input 시작 포인터 이동

            if (l.size == 1) {                                                      /// filter size가 1일 때
                b = im;
            } else {
                im2col_cpu(im, l.c/l.groups, l.h, l.w, l.size, l.stride, l.pad, b); /// image를 columns로 바꾸어준다.
            }
            gemm(0,0,m,n,k,1,a,k,b,n,1,c,n);                                        /// 실질적인 계산이 이루어진다. ***
        }
    }

    if(l.batch_normalize){                                                          /// batch normalization 실행 유무
        forward_batchnorm_layer(l, net);                                            /// batch normalization 실행
    } else {
        add_bias(l.output, l.biases, l.batch, l.n, l.out_h*l.out_w);                /// convolution에서 그냥 0값인거 같다.
    }

    activate_array(l.output, l.outputs*l.batch, l.activation);                      /// activation function 적용
    if(l.binary || l.xnor) swap_binary(&l);                                         /// binary 일때 변환
}
```

## swap binary

```
void swap_binary(convolutional_layer *l)
{
    float *swap = l->weights;
    l->weights = l->binary_weights;
    l->binary_weights = swap;

#ifdef GPU
    swap = l->weights_gpu;
    l->weights_gpu = l->binary_weights_gpu;
    l->binary_weights_gpu = swap;
#endif
}
```

## add_bias

```
void add_bias(float *output, float *biases, int batch, int n, int size)
{
    int i,j,b;
    for(b = 0; b < batch; ++b){
        for(i = 0; i < n; ++i){
            for(j = 0; j < size; ++j){
                output[(b*n + i)*size + j] += biases[i];
            }
        }
    }
}
```

bias에 값을 더한다.
