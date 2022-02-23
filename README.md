# EPS32

第一步：安装准备：
【1】Centos7: 
sudo yum -y update && sudo yum install git wget flex bison gperf python3 python3-pip python3-setuptools cmake ninja-build ccache dfu-util libusbx



xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun

则必须安装 XCode 命令行工具，可运行 xcode-select--install命令进行安装


安装python3

python--version
如果输出结果是 Python2.7.17，则代表您的默认解析器是 Python 2.7。这时需要您运行以下命令检查电脑上是否已经安装过 Python 3:

https://brew.sh/    （2）
brew install python3

python3 --version

如果运行上述命令出现错误，则代表电脑上没有安装 Python 3。

【2】获取 ESP-IDF

mkdir -p ~/esp
cd ~/esp
git clone --recursive https://github.com/espressif/esp-idf.git

设置工具：
cd ~/esp/esp-idf
./install.sh esp32


通过一次性指定多个目标，可为多个目标芯片同时安装工具，如运行 ./install.shesp32,esp32c3,esp32s3。 通过运行 ./install.sh或 ./install.shall可一次性为所有支持的目标芯片安装工具。

ESP-IDF 工具安装器会下载 Github 发布版本中附带的一些工具，如果访问 Github 较为缓慢，可以设置一个环境变量，从而优先选择 Espressif 的下载服务器进行 Github 资源下载。



cd ~/esp/esp-idf
export IDF_GITHUB_ASSETS="dl.espressif.com/github_assets"
./install.sh


设置环境变量： 
请在需要运行 ESP-IDF 的终端窗口运行以下命令：
 
.  $HOME/esp/esp-idf/export.sh

现在，您可以开始准备开发 ESP32 应用程序了。您可以从 ESP-IDF 中 examples 目录下的 get-started/hello_world 工程开始。

在 Linux 中添加用户到 dialout
当前登录用户应当可以通过 USB 对串口进行读写操作。在多数 Linux 版本中，您都可以通过以下命令，将用户添加到 dialout组，从而获许读写权限:
sudo usermod -a -G dialout $USER

【3】创建工程：

cd ~/esp
cp -r $IDF_PATH/examples/get-started/hello_world .


连接设备：
Linux 操作系统： 以 /dev/tty开始




运行终端，配置在上述步骤中确认的串口：波特率 = 115200，数据位 = 8，停止位 = 1，奇偶校验 = N。以下截屏分别展示了如何在 Windows 和 Linux 中配置串口和上述通信参数（如 115200-8-1-N）。注意，这里一定要选择在上述步骤中确认的串口进行配置。

>请进入 hello_world目录

cd ~/esp/hello_world
idf.py set-target esp32
idf.py menuconfig


打开一个新工程后，应首先使用 idf.pyset-target esp32设置“目标”芯片

您终端窗口中显示出的菜单颜色可能会与上图不同。您可以通过选项 --style来改变外观。请运行 idf.py menuconfig  --help命令，获取更多信息。

【3】编译工程：
idf.py build

如果编译正常，则生成.bin文件


烧录到设备：
请使用以下命令，将刚刚生成的二进制文件 (bootloader.bin、partition-table.bin 和 hello_world.bin) 烧录至您的 ESP32 开发板：
idf.py -p PORT [-b BAUD] flash
请将 PORT 替换为 ESP32 开发板的串口名称。
您还可以将 BAUD 替换为您希望的烧录波特率。默认波特率为 460800。

注解
勾选 flash选项将自动编译并烧录工程，因此无需再运行 idf.pybuild。


【4】监视输出
您可以使用 idf.py-pPORTmonitor命令，监视 “hello_world” 工程的运行情况。注意，不要忘记将 PORT 替换为您的串口名称。
运行该命令后，IDF 监视器应用程序将启动：:
$ idf.py -p <PORT> monitor
Running idf_monitor in directory [...]/esp/hello_world/build
Executing "python [...]/esp-idf/tools/idf_monitor.py -b 115200 [...]/esp/hello_world/build/hello_world.elf"...
--- idf_monitor on <PORT> 115200 ---
--- Quit: Ctrl+] | Menu: Ctrl+T | Help: Ctrl+T followed by Ctrl+H ---
ets Jun  8 2016 00:22:57
rst:0x1 (POWERON_RESET),boot:0x13 (SPI_FAST_FLASH_BOOT)
ets Jun  8 2016 00:22:57
...
此时，您就可以在启动日志和诊断日志之后，看到打印的 “Hello world!” 了。
...
    Hello world!
    Restarting in 10 seconds...
    This is esp32 chip with 2 CPU core(s), WiFi/BT/BLE, silicon revision 1, 2MB external flash
Minimum free heap size: 298968 bytes
    Restarting in 9 seconds...
    Restarting in 8 seconds...
    Restarting in 7 seconds...
您可使用快捷键 Ctrl+]，退出 IDF 监视器。

您可使用快捷键 Ctrl+]，退出 IDF 监视器。
如果 IDF 监视器在烧录后很快发生错误，或打印信息全是乱码（如下），很有可能是因为您的开发板采用了 26 MHz 晶振，而 ESP-IDF 默认支持大多数开发板使用的 40 MHz 晶振。

此时，您可以：
	1. 退出监视器。
	2. 返回 menuconfig。
	3. 进入 Componentconfig–> ESP32-specific–> MainXTALfrequency进行配置，将 CONFIG_ESP32_XTAL_FREQ_SEL设置为 26 MHz。
	4. 重新 编译和烧录 应用程序。
>

您也可以运行以下命令，一次性执行构建、烧录和监视过程：
idf.py -p PORT flash monitor

idf.py --version

建议：更新 ESP-IDF
乐鑫会不时推出新版本的 ESP-IDF，修复 bug 或提供新的功能。请注意，EESP-IDF 的每个主要版本和次要版本都有相应的支持期限。支持期限满后，版本停止更新维护，用户可将项目升级到最新的 ESP-IDF 版本。更多关于支持期限的信息，请参考 ESP-IDF 版本。
因此，您在使用时，也应注意更新您本地的版本。最简单的方法是：直接删除您本地的 esp-idf文件夹，然后按照 第二步：获取 ESP-IDF中的指示，重新完成克隆。
另一种方法是仅更新变更的部分。具体方式，请前往 更新 ESP-IDF章节查看。具体更新步骤会根据您使用的 ESP-IDF 版本有所不同。
注意，更新完成后，请再次运行安装脚本，以防新版 ESP-IDF 所需的工具也有所更新。具体请参考 第三步：设置工具。
一旦重新安装好工具，请使用导出脚本更新环境，具体请参考 第四步：设置环境变量。

