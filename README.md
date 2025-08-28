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
adb shell am dumpheap com.lizhi.pplive /data/local/tmp/1.hprof  #hprof尾缀才能读

https://cs.android.com/android/platform/superproject/+/android-8.1.0_r9:frameworks/base/core/java/android/view/View.java

./record_android_trace -o trace_file.perfetto-trace -t 30s -b 64mb -a com.lizhi.pplive \
sched freq idle am wm gfx view binder_driver hal dalvik camera input res memory
这份帮助文档的操作说明翻译如下：

可选参数：
- `-h, --help` 显示帮助信息并退出
- `-o OUT, --out OUT` 输出文件或目录（默认：~/traces/）
- `-n, --no-open` 不打开或提供追踪数据
- `--no-open-browser` 不在浏览器中打开，但仍提供追踪数据（适用于远程使用）
- `--origin ORIGIN` 用于打开追踪文件的网页地址
- `--sideload` 强制使用侧载的二进制文件而不是系统守护进程
- `--sideload-path SIDELOAD_PATH` 侧载指定的二进制文件而不是下载。隐含使用 `--sideload`
- `--no-guardrails` 忽略任何可能存在的追踪保护措施
- `-u, --user` 不运行 `adb root`，以用户身份运行（仅在侧载时）
- `--serial SERIAL, -s SERIAL` 指定 ADB 设备序列号

短选项：（仅在不使用 `-c/--config` 时）
- `-t TIME, --time TIME` 追踪时长 N[s,m,h]（默认：追踪直到停止）
- `-b BUFFER, --buffer BUFFER` 环形缓冲区大小 N[mb,gb]（默认：32mb）
- `-a com.myapp, --app com.myapp` Android（atrace）应用名称。可以指定多个。
- `-a*` 对所有应用（不加空格，避免 bash 扩展）
- `--list` 列出所有可用的类别
- `--list-ftrace` 列出所有 ftrace 事件

完整追踪配置（仅在不使用短选项时）：
- `-c CONFIG, --config CONFIG` 可以通过 [Perfetto 配置生成器](https://ui.perfetto.dev/#!/record) 生成
- `--bin` 将输入从 `--config` 解析为二进制 proto（默认：解析为文本）
- `--reporter-api` 通过追踪报告 API 传递追踪数据。仅在使用完整追踪配置 `-c` 时与报告包名 `'android.perfetto.cts.reporter'` 和报告类名 `'android.perfetto.cts.reporter.PerfettoReportService'`，并且设备上安装了报告器的情况下有效（参见 tools/install_test_reporter_app.py）。

长时间追踪：
如果要记录几小时的追踪并将其流式传输到文件中，需要传递完整的追踪配置并设置 `write_into_file = true`。请参见 [Perfetto 文档](https://perfetto.dev/docs/concepts/config#long-traces) 。

系统10及以上使用下面UI-Trace
adb shell perfetto -o /data/misc/perfetto-traces/trace.perfetto-trace \
    -t 10s \
    sched freq idle am wm gfx view binder_driver

adb pull /data/misc/perfetto-traces/trace.perfetto-trace
