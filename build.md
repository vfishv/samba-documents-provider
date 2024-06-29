功能：扫描、发现局域网内其他设备的共享

系统环境
ubuntu16.04

开源项目地址:
https://github.com/google/samba-documents-provider

Ubuntu 16.04下搭建 Android 开发环境 -JDK, Android Studio 安装
http://blog.csdn.net/meiguanggan/article/details/51443679

提示操作步骤:

1.下载Samba 4.5.1源码，与samba-documents-provider-master并列
https://download.samba.org/pub/samba/stable/samba-4.5.1.tar.gz

2.切换道samba-4.5.1源码目录

cd samba-4.5.1

３.创建git仓库

git init

4.应用补丁

git apply ../samba-documents-provider-master/sambapatch.diff

5.下载NDK r15b

http://blog.csdn.net/gyh198/article/details/75036686

6.切换到samba-4.5.1目录，对configure.sh进行修改(tips :set nu显示行号)
vim configure.sh
先修改本地NDK路径(使用绝对路径)
5 NDK=$ANDROID_SDK/root/Android/android-ndk-r15c
12 TOOLCHAIN=$CWD/root/Android/android-ndk-r15c/toolchain
51 $NDKAndroid/root/Android/android-ndk-r15c/build/tools/make_standalone_toolchain.py
再根据Flags解注释以编译想要的版本
 33 # Flags for x86_64
 34 ABI=x86_64
 35 PLATFORM_ARCH=x86_64

 36 TRIPLE=x86_64-linux-android

tips:如果编译ARMv7，需要加上ARM的
# Flags for 32-bit ARM
ABI=arm-linux-androideabi
PLATFORM_ARCH=arm

TRIPLE=arm-linux-androideabi

# Flags for ARM v7 used with flags for 32-bit ARM to compile for ARMv7
COMPILER_FLAG="-march=armv7-a -mfloat-abi=softfp -mfpu=vfpv3-d16"
LINKER_FLAG="-march=armv7-a -Wl,--fix-cortex-a8"
 

7.运行./configure.sh进行配置

8.运行./compile.sh 编译出libsmbclient.so文件

9.运行./install.sh ../samba-documents-provider-master/app/src/main/jniLibs/x86_64生成.so文件

10.切换到SambaDocumentsProvider目录
cd ../samba-documents-provider-master/
(如果有缓存，先运行rm -r app/src/main/cpp/samba_includes)

运行mv app/src/main/jniLibs/x86_64/includes app/src/main/cpp/samba_includes

11.切换到Samba源码目录
cd ../samba-4.5.1/

运行make distclean

12.从androidstudio中打开samba-documents-provider-master项目
修改build.gradle
ndk {
//            abiFilters 'armeabi-v7a'
//            abiFilters 'arm64-v8a'
//            abiFilters 'x86'
            abiFilters 'x86_64'

        }

13.androidstudio下进行编译，提示JniHelper.cc下ENODEV报错；

需要在AndroidStudio中配置NDK路径，File->ProjectStructure…->在打开的窗口中左侧选中SDKLocation->在右侧Android NDK Location中填入NDK目录所在路径

14.编译生成apk

提示：编译生成的apk在android6.0及以上模拟器或真机设备(armeabi-v7a，arm64-v8a，x86，x86_64)上运行一切正常，但在6.0以下均无法运行，提示UnsatisfiedLinkError，找不到动态库libsamba_client.so

替代方案：当前android5.1上采用的是jcifs源码，以java代码的形式来实现的

官方网站
http://jcifs.samba.org/