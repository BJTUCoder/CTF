# crack the apk
> 
点击ping， 延迟1s， 点击pong, 延迟1s。
点击100万次

## Android.mk
    LOCAL_PATH:= $(call my-dir)

    include $(CLEAR_VARS)
    LOCAL_CFLAGS += -pie -fPIE
    LOCAL_LDFLAGS += -pie -fPIE
    LOCAL_SRC_FILES:= ping.c
    LOCAL_C_INCLUDES:= ping.h
    LOCAL_MODULE:= ping
    LOCAL_LDLIBS := libpp.so
    include $(BUILD_EXECUTABLE)

## ping.h 
    #include <stdio.h>
    int Java_com_geekerchina_pingpongmachine_MainActivity_ping(int a1, int a2, int a3, signed int a4);
    int Java_com_geekerchina_pingpongmachine_MainActivity_pong(int a1, int a2, int a3, int a4);
    
## ping.c 
    #include <ping.h>
    
    java代码 逻辑
    
    ...
    
## NDK编译 
将so放置到该目录下，然后ndk编译

## 运行
将libpp.so 和 生成的 ping  拷贝到手机中， 添加手机so环境变量以便手机可以找到so文件。 

## OVER
