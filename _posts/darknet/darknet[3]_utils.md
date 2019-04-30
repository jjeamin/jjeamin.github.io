# /src/utils.c

## strip

```
void strip(char *s)
{
    size_t i;
    size_t len = strlen(s);
    size_t offset = 0;
    for(i = 0; i < len; ++i){
        char c = s[i];
        if(c==' '||c=='\t'||c=='\n') ++offset;
        else s[i-offset] = c;
    }
    s[len-offset] = '\0';
}
```

- cfgfile에서 공백,줄바꿈,tab을 제거한다.

## fgetl
```
int feof(FILE *stream) : 파일의 끝에 도달했는지의 여부
char fgets(char *string, int n, FILE *stream) : 파일을 한줄씩 읽어들이는 함수
```

```
char *fgetl(FILE *fp)
{
    if(feof(fp)) return 0;
    size_t size = 512;
    char *line = malloc(size*sizeof(char));
    if(!fgets(line, size, fp)){ /// 파일을 한줄 읽고 파일이 끝인지 아닌지 판단
        free(line);             /// malloc 해제
        return 0;               /// return 0
    }

    size_t curr = strlen(line); /// 한줄의 문자열 길이

    while((line[curr-1] != '\n') && !feof(fp)){ 
        if(curr == size-1){
            size *= 2;
            line = realloc(line, size*sizeof(char));
            if(!line) {
                printf("%ld\n", size);
                malloc_error();
            }
        }
        size_t readsize = size-curr;
        if(readsize > INT_MAX) readsize = INT_MAX-1;
        fgets(&line[curr], readsize, fp);
        curr = strlen(line);
    }
    if(line[curr-1] == '\n') line[curr-1] = '\0';

    return line;
}
```
