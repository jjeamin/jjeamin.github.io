# convolution layer

`convolution layer` 을 어떻게 구현했는지 알기전에 `convolution` 과 `convolution layer` 가 무엇인지 알아보자.

## convolution

convolution은 합성곱이라고 정의를 할 수 있다. 합성곱을 쉽게 이해하기 위해서 이런 예시를 자주 사용하는 것 같아서 나도 직접 적어보며 이해해보기로 했다. 1차원에서 공을 떨어뜨리고 그 위치에서 공을 다시 집어서 한번 더 떨어뜨릴때 원래 떨어진 지점인 a에 도달할 확률은 확률분포를 f라고 본다면 f(a)라고 생각 할 수 있다.



![conv1](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/convolution_1.PNG)



다시 시작점을 b로 설정하고 확률분포를 g라고 본다면 g(b)가 된다. 고정된 값인 최종 거리 c를 구하려면 `a+b=c`이고 a와 b에 도달할 확률이 고정되어 있으므로 c에 도달할 확률은 `f(a)*g(b)`로 나타낼수 있다.

*예를 들어 a가 0이고 c가 3일 경우에 b는 3이되고 a가 1이면 b가 2가된다. 결국 최종거리만 맞으면 된다는 것이다.*

이렇게 나올수 있는 모든 c에 도달할 확률을 더한 것이 `convolution`이다.

## convolution layer

convolution layer는 보통 딥러닝에서 이미지를 처리하는 모델에서 많이 사용하는 layer다. convolution layer는 image에 filter를 옮겨가며 합성곱을 하여 이미지의 특징을 뽑아내주는 layer다. 보통 뒤에 pooling layer와 같이 사용된다.

---

## /src/parser.c/parse_convolutional

```
convolutional_layer parse_convolutional(list *options, size_params params)
{
    int n = option_find_int(options, "filters",1);
    int size = option_find_int(options, "size",1);
    int stride = option_find_int(options, "stride",1);
    int pad = option_find_int_quiet(options, "pad",0);
    int padding = option_find_int_quiet(options, "padding",0);
    int groups = option_find_int_quiet(options, "groups", 1);
    if(pad) padding = size/2;

    char *activation_s = option_find_str(options, "activation", "logistic");
    ACTIVATION activation = get_activation(activation_s);

    int batch,h,w,c;
    h = params.h;
    w = params.w;
    c = params.c;
    batch=params.batch;
    if(!(h && w && c)) error("Layer before convolutional layer must output image.");
    int batch_normalize = option_find_int_quiet(options, "batch_normalize", 0);
    int binary = option_find_int_quiet(options, "binary", 0);
    int xnor = option_find_int_quiet(options, "xnor", 0);

    convolutional_layer layer = make_convolutional_layer(batch,h,w,c,n,groups,size,stride,padding,activation, batch_normalize, binary, xnor, params.net->adam);
    layer.flipped = option_find_int_quiet(options, "flipped", 0);
    layer.dot = option_find_float_quiet(options, "dot", 0);

    return layer;
}
```

convolution section을 파싱하는 부분이다. `convolutional_layer`는 layer 구조체이고 convolution에 필요한 매개변수들을 구조체에 넣어준다.

- filters : 필터의 개수 = n
- size : 필터 크기 `size x size`
- stride : 필터를 몇 픽셀 움직일 것인가
- pad : 이미지 주변을 특정 숫자(보통 0)로 채워서 이미지의 size를 증가시켜준다.
- padding : pad랑 같은거 같다..?
- groups : filter groups = batch?
- batch_normalize : batch normalization 사용 유무
- binary : 이진화 (모델 크기 감소 영향)
- xnor : 이진화 (모델 크기 감소 영향)

- flipped : ?
- dot : ?

이진화 기법도 사용된 것을 볼 수 있다.

---

## /src/parser.c/make_convolutional_layer

```
convolutional_layer make_convolutional_layer(int batch, int h, int w, int c, int n, int groups, int size, int stride, int padding, ACTIVATION activation, int batch_normalize, int binary, int xnor, int adam)
{
    int i;
    convolutional_layer l = {0};
    l.type = CONVOLUTIONAL;

    l.groups = groups;
    l.h = h;
    l.w = w;
    l.c = c;
    l.n = n;
    l.binary = binary;
    l.xnor = xnor;
    l.batch = batch;
    l.stride = stride;
    l.size = size;
    l.pad = padding;
    l.batch_normalize = batch_normalize;

    l.weights = calloc(c/groups*n*size*size, sizeof(float));           /// weight(filter) 크기 할당
    l.weight_updates = calloc(c/groups*n*size*size, sizeof(float));

    l.biases = calloc(n, sizeof(float));                              /// bias 크기 할당
    l.bias_updates = calloc(n, sizeof(float));

    l.nweights = c/groups*n*size*size;                                /// weight의 수
    l.nbiases = n;                                                    /// bias의 수

    // float scale = 1./sqrt(size*size*c);
    float scale = sqrt(2./(size*size*c/l.groups));                    /// random weight initialization
    //printf("convscale %f\n", scale);
    //scale = .02;
    //for(i = 0; i < c*n*size*size; ++i) l.weights[i] = scale*rand_uniform(-1, 1);
    for(i = 0; i < l.nweights; ++i) l.weights[i] = scale*rand_normal();
    int out_w = convolutional_out_width(l);                           /// convolution filter를 통과한 image 폭
    int out_h = convolutional_out_height(l);                          /// convolution filter를 통과한 image 높이
    l.out_h = out_h;
    l.out_w = out_w;
    l.out_c = n;
    l.outputs = l.out_h * l.out_w * l.out_c;                          /// output image 크기
    l.inputs = l.w * l.h * l.c;                                       /// input image 크기

    l.output = calloc(l.batch*l.outputs, sizeof(float));              /// 한번 학습때 사용되는 output image 크기 할당
    l.delta  = calloc(l.batch*l.outputs, sizeof(float));              

    l.forward = forward_convolutional_layer;                          /// 학습
    l.backward = backward_convolutional_layer;
    l.update = update_convolutional_layer;
    if(binary){                                                       /// binary 사용시
        l.binary_weights = calloc(l.nweights, sizeof(float));
        l.cweights = calloc(l.nweights, sizeof(char));
        l.scales = calloc(n, sizeof(float));
    }
    if(xnor){                                                         /// xnor 사용시
        l.binary_weights = calloc(l.nweights, sizeof(float));
        l.binary_input = calloc(l.inputs*l.batch, sizeof(float));
    }

    if(batch_normalize){                                              /// batch normalize 사용시
        l.scales = calloc(n, sizeof(float));
        l.scale_updates = calloc(n, sizeof(float));
        for(i = 0; i < n; ++i){
            l.scales[i] = 1;
        }

        l.mean = calloc(n, sizeof(float));
        l.variance = calloc(n, sizeof(float));

        l.mean_delta = calloc(n, sizeof(float));
        l.variance_delta = calloc(n, sizeof(float));

        l.rolling_mean = calloc(n, sizeof(float));
        l.rolling_variance = calloc(n, sizeof(float));
        l.x = calloc(l.batch*l.outputs, sizeof(float));
        l.x_norm = calloc(l.batch*l.outputs, sizeof(float));
    }
    if(adam){                                                       /// abam 사용시
        l.m = calloc(l.nweights, sizeof(float));
        l.v = calloc(l.nweights, sizeof(float));
        l.bias_m = calloc(n, sizeof(float));
        l.scale_m = calloc(n, sizeof(float));
        l.bias_v = calloc(n, sizeof(float));
        l.scale_v = calloc(n, sizeof(float));
    }

#ifdef GPU                                                          /// GPU 사용시
    l.forward_gpu = forward_convolutional_layer_gpu;
    l.backward_gpu = backward_convolutional_layer_gpu;
    l.update_gpu = update_convolutional_layer_gpu;

    if(gpu_index >= 0){
        if (adam) {
            l.m_gpu = cuda_make_array(l.m, l.nweights);
            l.v_gpu = cuda_make_array(l.v, l.nweights);
            l.bias_m_gpu = cuda_make_array(l.bias_m, n);
            l.bias_v_gpu = cuda_make_array(l.bias_v, n);
            l.scale_m_gpu = cuda_make_array(l.scale_m, n);
            l.scale_v_gpu = cuda_make_array(l.scale_v, n);
        }

        l.weights_gpu = cuda_make_array(l.weights, l.nweights);
        l.weight_updates_gpu = cuda_make_array(l.weight_updates, l.nweights);

        l.biases_gpu = cuda_make_array(l.biases, n);
        l.bias_updates_gpu = cuda_make_array(l.bias_updates, n);

        l.delta_gpu = cuda_make_array(l.delta, l.batch*out_h*out_w*n);
        l.output_gpu = cuda_make_array(l.output, l.batch*out_h*out_w*n);

        if(binary){
            l.binary_weights_gpu = cuda_make_array(l.weights, l.nweights);
        }
        if(xnor){
            l.binary_weights_gpu = cuda_make_array(l.weights, l.nweights);
            l.binary_input_gpu = cuda_make_array(0, l.inputs*l.batch);
        }

        if(batch_normalize){
            l.mean_gpu = cuda_make_array(l.mean, n);
            l.variance_gpu = cuda_make_array(l.variance, n);

            l.rolling_mean_gpu = cuda_make_array(l.mean, n);
            l.rolling_variance_gpu = cuda_make_array(l.variance, n);

            l.mean_delta_gpu = cuda_make_array(l.mean, n);
            l.variance_delta_gpu = cuda_make_array(l.variance, n);

            l.scales_gpu = cuda_make_array(l.scales, n);
            l.scale_updates_gpu = cuda_make_array(l.scale_updates, n);

            l.x_gpu = cuda_make_array(l.output, l.batch*out_h*out_w*n);
            l.x_norm_gpu = cuda_make_array(l.output, l.batch*out_h*out_w*n);
        }
#ifdef CUDNN
        cudnnCreateTensorDescriptor(&l.normTensorDesc);
        cudnnCreateTensorDescriptor(&l.srcTensorDesc);
        cudnnCreateTensorDescriptor(&l.dstTensorDesc);
        cudnnCreateFilterDescriptor(&l.weightDesc);
        cudnnCreateTensorDescriptor(&l.dsrcTensorDesc);
        cudnnCreateTensorDescriptor(&l.ddstTensorDesc);
        cudnnCreateFilterDescriptor(&l.dweightDesc);
        cudnnCreateConvolutionDescriptor(&l.convDesc);
        cudnn_convolutional_setup(&l);
#endif
    }
#endif
    l.workspace_size = get_workspace_size(l);
    l.activation = activation;

    fprintf(stderr, "conv  %5d %2d x%2d /%2d  %4d x%4d x%4d   ->  %4d x%4d x%4d  %5.3f BFLOPs\n", n, size, size, stride, w, h, c, l.out_w, l.out_h, l.out_c, (2.0 * l.n * l.size*l.size*l.c/l.groups * l.out_h*l.out_w)/1000000000.);

    return l;
}
```

convolution을 종합해서 만들어주는 함수다. convolution layer의 구조체에 들어간 값들을 이용해 convolution layer를 완성시킨다.



![conv2](https://github.com/jjeamin/jjeamin.github.io/raw/master/_posts/post_img/darknet/convolution_2.PNG)



## src/convolutional_layer.c/convolutional_out_width

```
int convolutional_out_width(convolutional_layer l)
{
    return (l.w + 2*l.pad - l.size) / l.stride + 1;
}
```

- convolution을 통과한 output의 width

- `input width` + 2 * `pad` - `filter size` / `stride` + 1

## src/convolutional_layer.c/convolutional_out_height

```
int convolutional_out_height(convolutional_layer l)
{
    return (l.h + 2*l.pad - l.size) / l.stride + 1;
}
```

- convolution을 통과한 output의 height

- `input height` + 2 * `pad` - `filter size` / `stride` + 1

---

# 전파,역전파,업데이트

이 부분이 딥러닝의 가장 중요한 기초라고 생각되는 부분이기 때문에 자세히 살펴보도록 해야할 것 같다. 직접 c언어로 구현이 되어있기 때문에 분석하기 쉬울거 같다.

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

    int m = l.n/l.groups;                                                 /// filter group 개수
    int k = l.size*l.size*l.c/l.groups;                                   /// 여기서부터
    int n = l.out_w*l.out_h;                                              /// output 이미지 크기
    for(i = 0; i < l.batch; ++i){
        for(j = 0; j < l.groups; ++j){
            float *a = l.weights + j*l.nweights/l.groups;                 /// 배열에서 학습 시작 포인터 이동
            float *b = net.workspace;                                     /// workspace ??
            float *c = l.output + (i*l.groups + j)*n*m;                   /// output 위치 이동
            float *im =  net.input + (i*l.groups + j)*l.c/l.groups*l.h*l.w; /// 배열에서 input 시작 포인터 이동

            if (l.size == 1) {                                            /// filter size가 1일 때
                b = im;
            } else {
                im2col_cpu(im, l.c/l.groups, l.h, l.w, l.size, l.stride, l.pad, b); /// image를 columns로 바꾸어준다.
            }
            gemm(0,0,m,n,k,1,a,k,b,n,1,c,n);
        }
    }

    if(l.batch_normalize){
        forward_batchnorm_layer(l, net);
    } else {
        add_bias(l.output, l.biases, l.batch, l.n, l.out_h*l.out_w);
    }

    activate_array(l.output, l.outputs*l.batch, l.activation);
    if(l.binary || l.xnor) swap_binary(&l);
}
```










# 참조
- [https://dkeemin.com/%EC%BB%A8%EB%B3%BC%EB%A3%A8%EC%85%98convolution-%EC%A0%95%EC%9D%98%EB%A5%BC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%B4%EB%B3%B4%EC%9E%90/](https://dkeemin.com/%EC%BB%A8%EB%B3%BC%EB%A3%A8%EC%85%98convolution-%EC%A0%95%EC%9D%98%EB%A5%BC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%B4%EB%B3%B4%EC%9E%90/)

- [http://colah.github.io/posts/2014-07-Understanding-Convolutions/](http://colah.github.io/posts/2014-07-Understanding-Convolutions/)

- [https://brunch.co.kr/@chris-song/24](https://brunch.co.kr/@chris-song/24)
