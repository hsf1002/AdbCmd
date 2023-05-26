



# adb

```
https://adb.clockworkmod.com/
https://adbshell.com/downloads
```

##### 使用wireless方式连接ADB

当USB端口与adb无法同时存在时使用

```
1. adb tcpip 5555
2. adb connect 192.168.3.47:5555
3. adb devices
List of devices attached
76837775147826	device
192.168.3.47:5555	device
4. adb -s 76837775147826 shell
5. adb disconnect 192.168.3.47:5555
```

##### am
```
打开一个Activity
adb shell am start -n pck_name/class_name
adb shell am start pck_name/class_name -e key value
adb shell am start -a "android.intent.action,VIEW" -d "https://www.google.com"
```

```
应用启动耗时
adb shell am start -S -W com.android.settings/.Settings
另一种方法
06-29 10:23:55.781 I/ActivityTaskManager( 4559): Displayed com.android.settings/.Settings: +243ms
```

```
强制关闭一个应用
adb shell am force-stop pck_name
```

```
发送广播
adb shell am broadcast -a "bc_name"
adb shell am broadcast -a "bc_name" -e key value
adb shell am broadcast -a android.intent.action.BOOT_COMPILETED
```

```
启动服务
adb shell am startservice "pck_name/service_name"
```

##### dumpsys

```
查看当前Activity
adb shell dumpsys window   | grep mCurrentFocus
adb shell dumpsys activity | grep -i foc
adb shell dumpsys activity | grep "mResume"
adb shell dumpsys activity top | grep ACTIVITY | tail -n 1
```

```
查看当前Fragment
adb shell dumpsys activity top | grep '#[0-9]: ' | tail -n 1
```

```
adb shell dumpsys package	 // 输出很庞大，包括Permissions，Features，Activity Resolver Table等
adb shell dumpsys package packagename   // 获取某个应用信息
adb shell dumpsys window | grep "ShownFrame" // 查看屏幕分辨率
adb shell dumpsys window displays |head -n 3 // 查看屏幕宽高
adb shell dumpsys usagestats  // 每个界面启动时间
```

```
adb shell dumpsys activity intents  
adb shell dumpsys activity broadcasts  
adb shell dumpsys activity providers  
adb shell dumpsys activity services  
adb shell dumpsys activity activities  
adb shell dumpsys activity processes  
```

```
该应用必须处于活动状态
adb shell dumpsys meminfo packagename or PID  
adb shell dumpsys meminfo  
adb shell dumpsys cpuinfo  
adb shell dumpsys wifi  
adb shell dumpsys battery  
adb shell dumpsys batterystats
adb shell dumpsys statusbar  
adb shell dumpsys diskstats   
adb shell dumpsys power
adb shell dumpsys display
adb shell dumpsys gfxinfo
adb shell dumpsys alarm
adb shell dumpsys location
```

##### pm list 

```
adb shell pm list packages    // 列出系统所有包名，后面加上 -f 显示APP的路径
adb shell pm list features    // 列出目标设备上的所有feature
adb shell pm list permissions // 列出目标平台上的所有权限

安装路径
adb shell pm path "com.android.settings"
adb shell pm list packages -f | grep "com.android.settings"
```

##### Settings
```
adb shell settings get system screen_brightness   30   // 获取当前亮度值 
adb shell settings put system screen_brightness   150  // 更改亮度值（亮度值在0—255之间）
adb shell settings get system screen_off_timeout  15000   // 获取屏幕休眠时间


adb shell settings get Global stay_on_while_plugged_in     // 充电时是否亮屏
adb shell settings put Global stay_on_while_plugged_in  0  // 充电时不要亮屏
```

##### OEM unlock
```
mount -o remount,rw /

1. 在开发者中打开OEM unlocking和usb debugging
2. adb reboot bootloader
3. fastboot flashing unlock
4. 根据提示按键解锁
5. fastboot reboot
6. adb root
7. adb disable-verity
8. adb reboot
9. adb root
10. adb remount
```

##### input

按键-单击：

```
adb shell input keyevent 26      (26-PowerKey， 82-解锁屏幕)  
```

按键-字符串：

```
adb shell input text hsf1002
```

TP-单击：

```
adb shell input tap 100 200
```

TP-滑动：

```
adb shell input swipe 100 200 200 400
```

##### 屏幕截图

```
adb shell screencap /sdcard/screen.png
```

##### 录制视频

```
adb shell screenrecord /sdcard/demo.mp4
```

##### 日志

```
adb logcat -v time > log  // 保存
adb logcat -c     // 清除日志

内核log
adb shell dmesg  | cat /proc/kmsg
```

##### 属性

```
adb shell getprop ro.product.name
adb shell setprop ro.product.name  coolpad

adb shell watchprops   // 监控系统属性

global：所有的偏好设置对系统的所有用户公开，第三方APP有读没有写的权限
secure：安全性的用户偏好系统设置，第三方APP有读没有写的权限
system：包含各种各样的用户偏好系统设置

adb shell settings get system "finger_home"
adb shell settings put system "finger_home" 1
adb shell settings list global
```

##### 系统签名

1. 使用系统命令

```
java -jar signapk.jar platform.x509.pem platform.pk8 MyDemo.apk MyDemo_signed.apk

java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" -jar out/host/linux-x86/framework/signapk.jar build/target/product/security/platform.x509.pem build/target/product/security/platform.pk8 MyDemo.apk MyDemo_signed.apk
```
2. 修改Android.mk文件

```
LOCAL_CERTIFICATE := platform
```

```
生成keystore：
keytool -genkey -alias sky.keystore -keyalg RSA -validity 20000 -keystore sky.keystore  （密钥：123456）

cd C:\Program Files\Java\jdk1.7.0_15\bin 
copy apk to this directory
重签名：
jarsigner -verbose -keystore sky.keystore -signedjar Tempal_packing_sign.apk Tempal_packing.apk sky.keystore
```

##### 反编译

```
java -jar apktool.jar d XXX.apk
```

报错：

```
java -jar apktool.jar b Factory1 -o Factory_21010511.apk
I: Using Apktool 2.5.1-e25c0f-SNAPSHOT
I: Checking whether sources has changed...
I: Smaling smali folder into classes.dex...
I: Checking whether resources has changed...
I: Building resources...
W: /home/hefeng/software/ApkTool/Factory1/res/layout-v26/abc_screen_toolbar.xml:5: error: No resource identifier found for attribute 'keyboardNavigationCluster' in package 'android'
W: 
brut.androlib.AndrolibException: brut.common.BrutException: could not exec (exit code = 1): [/tmp/brut_util_Jar_74022679766604650467241449429402703582.tmp, p, --forced-package-id, 127, --min-sdk-version, 23, --target-sdk-version, 29, --version-code, 7, --version-name, 1.8.0(201028_1432), --no-version-vectors, -F, /tmp/APKTOOL1236562404885088240.tmp, -e, /tmp/APKTOOL8134476515707962018.tmp, -0, arsc, -I, /home/hefeng/.local/share/apktool/framework/1.apk, -S, /home/hefeng/software/ApkTool/Factory1/res, -M, /home/hefeng/software/ApkTool/Factory1/AndroidManifest.xml]
```

安装framework-res.apk：

```
./apktool install-framework /home/work/S809_AUR-A0/ctcc/work/out/target/product/ud710_7h10/system/framework/framework-res.apk
```

重新打包：

```
java -jar apktool.jar b XXX -o YYY.apk(打包后要命名的名称) -o表示新生成的apk文件放在当前文件夹
```

##### 权限异常

ubuntu下无法连接真机，lsusb可以显示出vid, 但是adb devices，提示： ????????????	 no permissions

```
Bus 001 Device 009: ID 1ebf:5d24
```

~/.android/新建adb_usb.ini，将01ebf加入，打开`/etc/udev/rules.d/70-persistent-net.rules`加入：  

```
#common sprd adb setting
SUBSYSTEM=="usb", ATTRS{idVendor}=="1782", ATTRS{idProduct}=="5d24", MODE="0666"
#just for Coolpad
SUBSYSTEM=="usb", ATTRS{idVendor}=="1ebf", ATTRS{idProduct}=="5d24", MODE="0666"
```

```
adb shell  error: device not found
adb nodaemon server
netstat -ano | findstr "5037"
tasklist | findstr "7860"
taskkill /f /pid 7860
```

##### 安装

1. adb.exe,AdbWinApi.dll,AdbWinUsbApi.dll拷贝到：C:\Windows\System32(64位应该拷贝到C:Windows\SysWOW64)
2. 设置环境变量，添加到系统变量Path中
3. adb_usb.ini、adbkey、adbkey.pub拷贝到：C:\Users\用户名\.android，如：C:\Users\yoyo下不存在.android，需要手动新建.android文件夹
4. 快捷键Win+R输入cmd进入命令行，输入adb version，如显示“Android Debug Bridge version 1.0.32”表示adb安装成功

安装可能出现的问题：

1. `C:\Users\feng_he\.android`下新建adb_usb.ini，并将设备管理器中 Android Device、端口、人体学输入设备的VID添加，重新插拔USB线，输入adb kill-server再输入adb shell  

2. 如果提示`could not read ok from ADB Server, error = 109`  
    把环境变量 adb 添加到系统变量里面  

3. 如果提示`adb server version (31) doesn’t match this client (36); killing…`  
    可能`Windows\System32\adb.exe和Users\feng_he\AppData\Local\Android\sdk\platform-tools\adb.exe`太新了，这两处替换为旧的文件

4. 如果提示`Error:CreateProcess error=216, 该版本的 %1 与您运行的 Windows 版本不兼容`
    开发项目的引用Java jdk，与本机安装的java jdk版本不一致，打开项目后，在project structure里面更改一下本机的真实的Java jdk路径  

5. AVD 突然出现了`dev kvm is not found 这个错误`
    `C:\Users\Administrator\AppData\Local\Android\sdk\extras\intel\Hardware_Accelerated_Execution_Manager`重新安装  
