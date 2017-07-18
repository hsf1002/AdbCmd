# adb
adb common usage command

##### 环境配置
adb.exe,AdbWinApi.dll,AdbWinUsbApi.dll
三个文件copy到：C:\Windows\System32
新建环境变量：adb    C:\Users\feng_he\AppData\Local\Android\sdk\platform-tools
添加到path：%adb%

##### adb连接不上
C:\Users\feng_he\.android下新建adb_usb.ini，并将设备管理器中 Android Device、端口、人体学输入设备的VID添加，重新插拔USB线，输入adb kill-server再输入adb shell

##### 打开一个Activity
adb shell am start -n 包名/类名

##### 强制关闭一个应用
adb shell am force-stop package

##### 写日志
adb logcat -v time > log

##### 清除日志 
adb logcat -c

##### 查看当前Activity，当然打开某个APK后也可以查看其包名类名
adb shell dumpsys activity top | head -n 10  
dumpsys activity intents  
dumpsys activity broadcasts  
dumpsys activity providers  
dumpsys activity services  
dumpsys activity activities  
dumpsys activity processes  
dumpsys window  | head -n 50  

##### 仅列出包名
adb shell pm list packages

##### 列出一些系统信息和所有应用的信息。这个命令的输出很庞大，包括Features，Activity Resolver Table等
adb shell dumpsys packages

##### 查看某个应用当前内存使用情况，该应用必须处于活动状态
adb shell dumpsys meminfo packagename or PID  

adb shell dumpsys meminfo  
adb shell dumpsys cpuinfo  
adb shell dumpsys wifi  
adb shell dumpsys battery  
adb shell dumpsys statusbar  
adb shell dumpsys diskstats   

##### 获取安装包信息
adb shell dumpsys package packagename  

##### 每个界面启动时间
adb shell dumpsys usagestats  

##### 查看手机分辨率
adb shell dumpsys window | grep "ShownFrame"

##### 列出目标设备上安装的所有app的包名
adb shell pm list packages

##### 列出目标设备上的所有feature
adb shell pm list features

##### 列出目标平台上的所有权限
adb shell pm list permissions

##### 屏幕截图再从data中pull出来
adb shell
screencap -p | sed s/\r$// > data/screen.png

##### 按键事件
adb shell input keyevent 26      (26-PowerKey， 82-解锁屏幕)  

#####  TP单击
adb shell input tap 100 200
##### TP滑动
adb shell input swipe 100 200 200 400

##### 查看屏的IC
adb shell 	cat /proc/cmdline

##### 查看Flash ID
adb shell  	cd ./sys/bus/mmc/devices/mmc0:0001
cat manfid	# FLASH ID

##### 查看当前电量电压
cd /sys/class/power_supply/sprdfgu/   cat fgu_current
cd /sys/class/power_supply/battery     cat charger_voltage

##### 签名命令
java -jar signapk.jar platform.x509.pem platform.pk8 MyDemo.apk MyDemo_signed.apk
给APK签名，签名文件目录：build\target\product\security

##### 签名失败原因
INSTALL_FAILED_UPDATE_INCOMPATIBLE  INSTALL_FAILED_SHARED_USER_INCOMPATIBLE
adb 安装apk提示，apk的AndroidManifest.xml中声明了android:sharedUserId="android.uid.system"，但没有相应的签名，应与sharedUserId的应用使用一样的签名
INSTALL_FAILED_USER_RESTRICTED
用户被限制安装应用
