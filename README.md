AndroidRuntime: FATAL EXCEPTION
AndroidRuntime: Shutting down VM

FATAL,leaked, block,Pid id XXX,crash,DALVIK THREADS, OOM,unfinished died
查找应用包名--快速定位ANR

 The application may be doing too much work on its main thread.

AndroidRuntime(xxxx): Shutting down VM

线程的状态是native则表示当前正在执行jni,

调试gradle
创建配置
![screenshot-20240731-102415.png]()

./gradlew :app:assembleDebug -Dorg.gradle.daemon=false -Dorg.gradle.debug=true

dump内存
adb shell am dumpheap com.lizhi.pplive
