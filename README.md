# XPS-9360-hackintosh
hackintosh clover files for xps13-9360 with 8550U 
<br>

## 详细配置
Intel i7-8550U<br>
16GB RAM(SK Hynix)<br>
512GB ROM (samsung PM961)<br>
3200X1800 QHD Sharp<br>
DW1560 network card<br>
BIOS 2.9.0 <br>
MACOS 10.14.1 Mojave beta 4 <br>


## 安装
1、安装之前使用DVMT.efi调整DVMT参数,见[the-darkvoid](https://github.com/the-darkvoid/XPS9360-macOS)<br>
2、high sierra已经支持多种NVMe固态，如果安装过程中识别不到PM961或其他固态，一般有两种原因：<br>
一是未开启archi(进BIOS修改即可)；<br>
二是固态牌子是海力士建兴浦科特，解决办法自行搜索。<br>
我的固态会报IONVMeFamily.kext(2.1)的panic，采用了多种办法，最后最有效的是加一个boot参数dart=0，暂时还不明白是什么原理。遇到相同情况的可以试一试。
<br>
3、使用黑果小兵10.14.1镜像制作启动盘安装。<br>
4、安装过程中，第一次重启后，若安装过程中出现<br>
```
IOConsoleUsers: gIOScreenLockState 3, hs 0, bs 0, nov 0, sm 0x0
```
在clover界面的option的Graphic Injector中将injectIntel前面的对勾去掉。<br>
5、接下来就是安装到硬盘了，一般第一次安装到硬盘时会直接报错，这时候点击重新启动，再安装一次，这次会在安装结束时报错，不要紧，
直接将我的Clover文件拷贝到ESP分区的EFI文件夹下，设置好启动顺序，就可以顺利进入MAC系统了。<br>
6、接下来就是修改序列号、重建缓存以及修复屏幕等操作了这里不再赘述，~~其中注意的是，在今后的使用中，若开机时出现panic，
需要根据不同的报错，将对应的驱动从S/L/E中复制到Clover文件的/kexts/Others中，其他的不需要动，~~
这里不推荐使用KextUtility.app做转移驱动、重建缓存等操作，建议用命令行，因为这个kextutility.app这个软件已经很旧了，high sierra 和mojave一般要把驱动转移到/L*/E*里面，而不是/S*/L*/E*。下面介绍几个常用的命令：
<br>
>转移kext:  先cd到驱动所在目录，然后使用 sudo cp -R XXXX.kext /L*/E*<br>
>重建缓存：sudo kextcache -i /    
<br>
建议仅将键盘、触控板、声卡的驱动复制到/L*/E*里面，其他的不要动。注意这里是复制，Clover里也要保留。
<br>
7、关于声音的问题，参考了the-darkvoid在tonymacx86中很隐蔽的留言以及他在用git提交时很隐蔽的commit（滑稽），声卡驱动有a,b两种方式：
<br>
a.使用XPS9360.sh,它会自动注入codecommonder.kext和AppleHDA-ALC256.kext，你只要在ACPI里的SSDT-Config.aml中注入layoutid为2即可，同时删除kext/Other/AppleALC.kext。如果不改SSDT-Config.aml，会注入1,是没有声音的。这种方法会出现轻微的爆音，但其他方面都很完美，我不知道是不是只有我存在这个问题。
<br>
b.如果使用kext/Other里的AppleALC.kext，就需要注入layout-id为56，但是我发现用config.plist注入56是没用的，我又尝试在SSDT-config.aml中修改layout-id为56，此时layout-id依然为1，然而多了一个叫做layout-id-audio的系统变量，此时同样是没有声音的。所以我猜测可能XPS9360是不能用普通的方式注入layout-id的。但是按常理来讲，仿冒AppleALC这种方法应该式最完美的，虽然没有见过，但我猜测体验接近原生。但目前还不知道如何实现。
<br>
关于其他的注入方法，如果各位有妙招，欢迎留言。


## 相对于the-darkvoid的文件的修改：
1、添加IE Capitan主题<br>
2、删除ACPI/patched/SSDT-NVMe.aml<br>
3、在config.plist添加<br>
```
<key>Scan</key>
<dict>
<key>Entries</key>
<true/>
<key>Legacy</key>
<false/>
<key>Tool</key>
<false/>
</dict>
```
以隐藏多余启动项，需要显示按F3即可<br>
4、在KernelToPatch中添加相关代码去掉lilu输出信息以查看panic原因。<br>
5、可以删除VoodooPS2Controller.kext，替换为AppleSmartTouchPad.kext(在kext文件夹里)。两者的操作习惯略有不同，option和command的位置是相反的。替换的主要原因是睡眠后在锁屏界面键盘无法使用，替换后解决。<br>
6、添加启动参数dart=0，防止IONVMeFamily的panic发生（目前还不清楚原因）。<br>
7、添加了-v(啰嗦模式)，不加的话，开机经常卡在进度条那里，加了之后反而没事了。<br>
8、开启了fast模式，没有clover界面，直接开机，这里请按需修改。<br>
9、修改drivers64UEFI中多个文件，解决了很多panic。<br>

## 致谢
**[the-darkvoid](https://github.com/the-darkvoid/XPS9360-macOS)**<br>
**[rehabman](https://github.com/RehabMan/patch-nvme)**<br>
**[ymmshi](https://github.com/ymmshi/XPS-9360)**<br>
