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
