# /src/option_list.c

## read_option

```
int read_option(char *s, list *options)
{
    size_t i;
    size_t len = strlen(s);
    char *val = 0;
    for(i = 0; i < len; ++i){       /// '=' 분리
        if(s[i] == '='){
            s[i] = '\0';
            val = s+i+1;
            break;
        }
    }
    if(i == len-1) return 0;        /// '=' 없을 때 0 리턴
    char *key = s;                  
    option_insert(options, key, val);     /// section에 option 넣기   
    return 1;
}
```

- section에 option들을 저장합니다. convolution section에 filter size를 넣는 형태

## option_insert

```
void option_insert(list *l, char *key, char *val)
{
    kvp *p = malloc(sizeof(kvp));
    p->key = key;     /// option 이름
    p->val = val;     /// option 값
    p->used = 0;      /// 사용유무
    list_insert(l, p);  
}
```

- section list에 option node를 추가

## option_find

```
char *option_find(list *l, char *key)
{
    node *n = l->front;
    while(n){
        kvp *p = (kvp *)n->val;
        if(strcmp(p->key, key) == 0){
            p->used = 1;
            return p->val;
        }
        n = n->next;
    }
    return 0;
}
```

- option에서 key를 찾는다. 예를 들어 option list에 batch를 찾는다면 key는 batch다.

## option_find_int

```
int option_find_int(list *l, char *key, int def)
{
    char *v = option_find(l, key);
    if(v) return atoi(v);
    fprintf(stderr, "%s: Using default '%d'\n", key, def);
    return def;
}
```

- int형 option의 key를 찾는다.(없을경우 default 값을 값으로 한다.)

## option_find_float

```
float option_find_float(list *l, char *key, float def)
{
    char *v = option_find(l, key);
    if(v) return atof(v);
    fprintf(stderr, "%s: Using default '%lf'\n", key, def);
    return def;
}
```

- float형 option의 key를 찾는다

## option_int_quiet

```
int option_find_int_quiet(list *l, char *key, int def)
{
    char *v = option_find(l, key);
    if(v) return atoi(v);
    return def;
}
```

- default값을 사용가능한 함수
