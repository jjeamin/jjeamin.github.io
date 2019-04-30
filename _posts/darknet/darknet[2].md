---
layout: post
title:  "Darknet 프로젝트 완전분석하기"
summary: "Darknet 프로젝트 완전분석하기"
date:   2019-04-28 12:00 -0400
categories: Darknet
---
# c언어 정리
- `extern void function()` : 다른 소스 파일에 있는 함수 사용
- `ifndef` ~ `endif` : 헤더파일을 여러번 include하는 것을 방지하거나 특정 이벤트에서만 발생시키게 해주는 조건문

# /example
darknet framework를 실행시키는 예제들을 모아놓은 폴더이다. 제일 흔하게 예시로 쓰이는 darknet.c와 detector.c를 먼저 살펴보고 나머지를 전부 살펴 볼 것이다.

# /example/derknet.c/main()
제일 먼저 main을 한번 살펴봐야 할 것 같다.

```c
int main(int argc, char **argv)
{
    //test_resize("data/bad.jpg");
    //test_box();
    //test_convolutional_layer();
    if(argc < 2){
        fprintf(stderr, "usage: %s <function>\n", argv[0]);
        return 0;
    }
    gpu_index = find_int_arg(argc, argv, "-i", 0);
    if(find_arg(argc, argv, "-nogpu")) {
        gpu_index = -1;
    }

#ifndef GPU
    gpu_index = -1;
#else
    if(gpu_index >= 0){
        cuda_set_device(gpu_index);
    }
#endif

    if (0 == strcmp(argv[1], "average")){
        average(argc, argv);
    } else if (0 == strcmp(argv[1], "yolo")){
        run_yolo(argc, argv);
    } else if (0 == strcmp(argv[1], "super")){
        run_super(argc, argv);
    } else if (0 == strcmp(argv[1], "lsd")){
        run_lsd(argc, argv);
    } else if (0 == strcmp(argv[1], "detector")){
        run_detector(argc, argv);
    } else if (0 == strcmp(argv[1], "detect")){
        float thresh = find_float_arg(argc, argv, "-thresh", .5);
        char *filename = (argc > 4) ? argv[4]: 0;
        char *outfile = find_char_arg(argc, argv, "-out", 0);
        int fullscreen = find_arg(argc, argv, "-fullscreen");
        test_detector("cfg/coco.data", argv[2], argv[3], filename, thresh, .5, outfile, fullscreen);
    } else if (0 == strcmp(argv[1], "cifar")){
        run_cifar(argc, argv);
    } else if (0 == strcmp(argv[1], "go")){
        run_go(argc, argv);
    } else if (0 == strcmp(argv[1], "rnn")){
        run_char_rnn(argc, argv);
    } else if (0 == strcmp(argv[1], "coco")){
        run_coco(argc, argv);
    } else if (0 == strcmp(argv[1], "classify")){
        predict_classifier("cfg/imagenet1k.data", argv[2], argv[3], argv[4], 5);
    } else if (0 == strcmp(argv[1], "classifier")){
        run_classifier(argc, argv);
    } else if (0 == strcmp(argv[1], "regressor")){
        run_regressor(argc, argv);
    } else if (0 == strcmp(argv[1], "isegmenter")){
        run_isegmenter(argc, argv);
    } else if (0 == strcmp(argv[1], "segmenter")){
        run_segmenter(argc, argv);
    } else if (0 == strcmp(argv[1], "art")){
        run_art(argc, argv);
    } else if (0 == strcmp(argv[1], "tag")){
        run_tag(argc, argv);
    } else if (0 == strcmp(argv[1], "3d")){
        composite_3d(argv[2], argv[3], argv[4], (argc > 5) ? atof(argv[5]) : 0);
    } else if (0 == strcmp(argv[1], "test")){
        test_resize(argv[2]);
    } else if (0 == strcmp(argv[1], "nightmare")){
        run_nightmare(argc, argv);
    } else if (0 == strcmp(argv[1], "rgbgr")){
        rgbgr_net(argv[2], argv[3], argv[4]);
    } else if (0 == strcmp(argv[1], "reset")){
        reset_normalize_net(argv[2], argv[3], argv[4]);
    } else if (0 == strcmp(argv[1], "denormalize")){
        denormalize_net(argv[2], argv[3], argv[4]);
    } else if (0 == strcmp(argv[1], "statistics")){
        statistics_net(argv[2], argv[3]);
    } else if (0 == strcmp(argv[1], "normalize")){
        normalize_net(argv[2], argv[3], argv[4]);
    } else if (0 == strcmp(argv[1], "rescale")){
        rescale_net(argv[2], argv[3], argv[4]);
    } else if (0 == strcmp(argv[1], "ops")){
        operations(argv[2]);
    } else if (0 == strcmp(argv[1], "speed")){
        speed(argv[2], (argc > 3 && argv[3]) ? atoi(argv[3]) : 0);
    } else if (0 == strcmp(argv[1], "oneoff")){
        oneoff(argv[2], argv[3], argv[4]);
    } else if (0 == strcmp(argv[1], "oneoff2")){
        oneoff2(argv[2], argv[3], argv[4], atoi(argv[5]));
    } else if (0 == strcmp(argv[1], "print")){
        print_weights(argv[2], argv[3], atoi(argv[4]));
    } else if (0 == strcmp(argv[1], "partial")){
        partial(argv[2], argv[3], argv[4], atoi(argv[5]));
    } else if (0 == strcmp(argv[1], "average")){
        average(argc, argv);
    } else if (0 == strcmp(argv[1], "visualize")){
        visualize(argv[2], (argc > 3) ? argv[3] : 0);
    } else if (0 == strcmp(argv[1], "mkimg")){
        mkimg(argv[2], argv[3], atoi(argv[4]), atoi(argv[5]), atoi(argv[6]), argv[7]);
    } else if (0 == strcmp(argv[1], "imtest")){
        test_resize(argv[2]);
    } else {
        fprintf(stderr, "Not an option: %s\n", argv[1]);
    }
    return 0;
}
```

수많은 예제파일을 실행시킬 수 있다. 위에서 부터 하나씩 찾아봐야 할 것 같다.
