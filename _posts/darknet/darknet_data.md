# DATA

`/src/data.c`

---

# Thread

## load_data_in_thread

```
pthread_t load_data_in_thread(load_args args) // third function when you use pthread in detector.c train_detector() function
{
    pthread_t thread;
    struct load_args *ptr = calloc(1, sizeof(struct load_args));
    *ptr = args;
    if(pthread_create(&thread, 0, load_thread, ptr)) error("Thread creation failed"); // call load_thread() function
    return thread;
}
```
