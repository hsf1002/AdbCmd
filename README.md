# adb
```
https://adb.clockworkmod.com/
```

##### 安装

1. adb.exe,AdbWinApi.dll,AdbWinUsbApi.dll拷贝到：C:\Windows\System32(64位应该拷贝到C:Windows\SysWOW64)

2. adb_usb.ini、adbkey、adbkey.pub拷贝到：C:\Users\用户名\.android，如：C:\Users\yoyo下不存在.android，需要手动新建.android文件夹

3. 快捷键Win+R输入cmd进入命令行，输入adb version，如显示“Android Debug Bridge version 1.0.32”表示adb安装成功

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

##### 打开一个Activity
```
adb shell am start -n 包名/类名
```

##### 强制关闭一个应用
```
adb shell am force-stop package
```

##### 保存日志
```
adb logcat -v time > log
```

##### 清除日志 
```
adb logcat -c
```

##### 查看内核log

```
dmesg  | cat /proc/kmsg
```

##### 查看当前Activity所属包名类名

```
adb shell dumpsys activity top | head -n 10  
adb shell dumpsys window | grep mCurrentFocus
```

##### dumpsys

```
adb shell dumpsys package	 // 输出很庞大，包括Permissions，Features，Activity Resolver Table等
adb shell dumpsys package packagename   // 获取某个应用信息
adb shell dumpsys window | grep "ShownFrame" // 查看手机分辨率
adb shell dumpsys usagestats  // 每个界面启动时间
```

```
adb shell dumpsys activity intents  
adb shell dumpsys activity broadcasts  
adb shell dumpsys activity providers  
adb shell dumpsys activity services  
adb shell dumpsys activity activities  
adb shell dumpsys activity processes  

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
adb shell pm list packages    // 列出系统所有包名
adb shell pm list features    // 列出目标设备上的所有feature
adb shell pm list permissions // 列出目标平台上的所有权限
```

##### 读写系统属性

```
adb shell getprop ro.product.name
adb shell setprop ro.product.name  coolpad
adb shell watchprops  // 动态查看系统属性
```

##### 屏幕截图
```
screencap -p data/screen.png
```

##### 操作按键

单击：

```
adb shell input keyevent 26      (26-PowerKey， 82-解锁屏幕)  
```

字符串：

```
adb shell input text hsf1002
```

#####  操作TP

单击：

```
adb shell input tap 100 200
```

滑动：

```
adb shell input swipe 100 200 200 400
```

##### 查看硬件信息

LCD IC：

```
adb shell cat /proc/cmdline
```

LCD name：

```
adb shell cat /sys/devices/platform/soc/soc:ap-ahb/20800000.dispc/lcd_name
```

TP ID：

```
adb shell cat /proc/bus/input/devices
```

TP name：

```
adb shell cat /sys/class/xr-tp/device/tp_version
```

固件：

```
adb shell cat /sys/touchscreen/firmware_version
```

Flash：

```
ID：
adb shell  	cd ./sys/bus/mmc/devices/mmc0:0001
cat manfid	# FLASH ID

Size：
/sys/block/mmcblk0/size
```

查看当前电量电压

```
cd /sys/class/power_supply/sprdfgu/   cat fgu_current
cd /sys/class/power_supply/battery    cat charger_voltage
```

##### 系统签名

1. 使用系统命令

```
java -jar signapk.jar platform.x509.pem platform.pk8 MyDemo.apk MyDemo_signed.apk
```
给APK签名，签名文件目录：`build\target\product\security`

```
java -Xmx2048m -Djava.library.path="out/host/linux-x86/lib64" -jar out/host/linux-x86/framework/signapk.jar build/target/product/security/platform.x509.pem build/target/product/security/platform.pk8 MyDemo.apk MyDemo_signed.apk
```

2. 修改Android.mk文件

```
LOCAL_CERTIFICATE := platform
```

签名失败原因：

`INSTALL_FAILED_UPDATE_INCOMPATIBLE  INSTALL_FAILED_SHARED_USER_INCOMPATIBLE`  
adb 安装apk提示，apk的AndroidManifest.xml中声明了android:sharedUserId="android.uid.system"，但没有相应的签名，应与sharedUserId的应用使用一样的签名  
`INSTALL_FAILED_USER_RESTRICTED`  
表明用户被限制安装应用

##### 安装失败原因

```
INSTALL_FAILED_UPDATE_INCOMPATIBLE  INSTALL_FAILED_SHARED_USER_INCOMPATIBLE
```

如上提示表明AndroidManifest.xml中声明了android:sharedUserId="android.uid.system"，但没有相应的签名，应与sharedUserId的应用使用一样的签名

```
INSTALL_FAILED_USER_RESTRICTED
```

表明用户被限制安装应用