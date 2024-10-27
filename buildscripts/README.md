# 构建

编译原生部分是一个独立于Gradle的过程，如果你跳过这个部分，应用程序将无法运行。

只有Linux或MacOS支持此操作， Windows (或是 WSL) 将**无法**运行。

原生部分编译过一次之后不做修改理论上不需要再次编译，只需要编译安卓APK就行了。

## 下载附件


`download.sh` 将会自动安装Android SDK, NDK并且下载需要的源码。

如果你在 Debian/Ubuntu 或是 RHEL/Fedora 上运行，它也会帮你安装必要的依赖项。

```sh
./download.sh
```

如果你已经安装了 Android SDK，你可以在脚本运行之前将 `android-sdk-linux` 符号链接到你的SDK根目录 
这样仍然会安装必要的SDK包。

这将会自动使用一个匹配的 NDK 版本 (在 SDK 内部) 或是下载并安装匹配版本的 NDK。

## Build

```sh
./buildall.sh
```

使用 `--clean` 参数运行 `buildall.sh` 将会在构建之前清理构建文件夹。
为了保证干净的构建，还可以事先运行`rm -rf prefix`。

默认情况下只会构建 32-bit ARM (`armv7l`) 版本.
你也许会想构建 AArch64, 或是 Intel x86.

为了构建其它版本，在运行./buildall.sh **之前**运行一个或多个以下命令：
```sh
./buildall.sh --arch arm64 mpv
./buildall.sh --arch x86 mpv
./buildall.sh --arch x86_64 mpv
```

# 开发

## 获取日志

```sh
adb logcat # 获取所有的日志, 当 drivers/vendor libs 输出到logcat时有用
adb logcat -s mpv # 只获取mpv的日志
```

## 重新构建单个组件

如果你只是改变了单一一个组件的代码 (例如 ffmpeg 或是 mpv) 并想要重新构建，你可以只运行 ./buildall.sh，或是使用以下指令重新构建单个组件

```sh
./buildall.sh -n ffmpeg
# 可选项: 添加 --clean 以从一个干净的状态开始建造
```

请注意，根据你的设备，你可能还需要重新构建其它架构 (例如 `--arch`)。

之后, 构建 mpv-android 并安装APK：

```sh
./buildall.sh -n
adb install -r ../app/build/outputs/apk/default/debug/app-default-universal-debug.apk
```

## 使用 Android Studio

您可以使用Android Studio开发代码库的Java部分。 在使用它之前，确保按照**构建**部分中的步骤至少构建项目一次。

你应该将 Android Studio 指向一个已安装的SDK，例如 `mpv-android/buildscripts/sdk/android-sdk-linux`.
然后点击 "Open an existing Android Studio project" 并且选择 `mpv-android`.

请注意，如果你从 Android Studio 构建，只会构建 Java/Kotlin 部分。
如果你对依赖代码 (ffmpeg, mpv, ...) 或是mpv-android原生代码 (`app/src/main/jni/*`) 进行了修改，请先使用以下脚本重新构建原生代码：

```sh
./buildall.sh -n
```

然后再使用 Android Studio 构建项目。
