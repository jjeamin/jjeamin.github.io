# NETWORK

`/src/network.c`

## make_network

```
network *make_network(int n)
{
    network *net = calloc(1, sizeof(network));
    net->n = n;                                           /// layer의 개수
    net->layers = calloc(net->n, sizeof(layer));          
    net->seen = calloc(1, sizeof(size_t));                
    net->t    = calloc(1, sizeof(int));
    net->cost = calloc(1, sizeof(float));
    return net;
}
```

- calloc : malloc은 메모리주소를 저장하는 반면 calloc은 값을 0으로 초기화 한다.

## load_network

```
network *load_network(char *cfg, char *weights, int clear)
{
    network *net = parse_network_cfg(cfg); // cfg파일 network구조체로 parsing
    if(weights && weights[0] != 0){
        load_weights(net, weights);        // weights parsing해서 저장하기
    }
    if(clear) (*net->seen) = 0;
    return net;
}
```

network의 구조와 weight 불러오기

## train_network

```c
float train_network(network *net, data d)
{
    assert(d.X.rows % net->batch == 0);
    int batch = net->batch;   // batch size
    int n = d.X.rows / batch; // n = 이미지의 개수 / batch size

    int i;
    float sum = 0;
    for(i = 0; i < n; ++i){ // 여기부터
        get_next_batch(d, batch, i*batch, net->input, net->truth);
        float err = train_network_datum(net); // 여기부터
        sum += err;
    }
    return (float)sum/(n*batch);
}
```

한 batch의 학습을 하는 과정

## get_current_batch

```
size_t get_current_batch(network *net)
{
    size_t batch_num = (*net->seen)/(net->batch*net->subdivisions);
    return batch_num;
}
```

현재 batch의 index

## train_network_datum

```
float train_network_datum(network *net)
{
    *net->seen += net->batch;
    net->train = 1;
    forward_network(net);
    backward_network(net);
    float error = *net->cost;
    if(((*net->seen)/net->batch)%net->subdivisions == 0) update_network(net);
    return error;
}
```

학습을 실질적으로 진행하는 함수

forward -> backward -> update (backpropagation) //여기부터

##
