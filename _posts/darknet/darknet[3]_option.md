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
