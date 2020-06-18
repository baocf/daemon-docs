Keil 命令行编译下载

------

> 参考资料:<http://www.keil.com/support/man/docs/uv4/uv4_commandline.htm>

编译：

```
UV4 〚command〛 〚projectfile〛 〚options〛
UV4.exe -r Blinky.uvproj -o Build_Output.txt

UV4.exe : 前面需要补全路径
-r Blinky.uvproj :Keil 工程名
-o Build_Output.txt:输出文档
```

下载：

```
UV4.exe -f Programming.UVPROJ -o Prg_Output.txt\
```

注意：keil 编译会自动打开Keil，也有可能打印在控制台，我没研究过。

------

#### IAR 命令行编译下载

------

> 参考资料：IAR帮助文档里面可以看到

编译：

```
IarBuild.exe test.ewp -build Debug -log all

IarBuild.exe :前面需要补全路径
test.ewp ：工程名，注意是.ewp结尾的。
-build Debug ：表示build
-log all ：打印所有，可以设置 -log info
```

下载: 在Setting 文件夹下面有一个.cspy的bat文件，在后面加上参数 "--download_only",前提是在IAR下面都配置好了，并且成功下载过一次的工程。

```
 --download_only
```

#### 使用

我们可以在工程所在的路径下面编写`bat`文件，通过直接运行bat就可以编译和下载了，第三方编辑器通常都可以配置编译选项和调试选项，也可以配置在Tool里面进行编译下载，我比较喜欢使用powershell进行编译和下载。







 ```powershell
eg:
@echo off
set IAR_ARM_WORKBENCH_PATH=C:\Program Files (x86)\IAR Systems\Embedded Workbench 8.3
set IAR_COMMON_PATH%IAR_ARM_WORKBENCH_PATH%\common\bin
set IAR_TOOLCHAIN_PATH%IAR_ARM_WORKBENCH_PATH%\arm\bin
set IAR_PRO_PATH=D:\...\..\..\..\iar\fd122.ewp

echo Init building...
::echo .>build_log.txt

%IAR_COMMON_PATH%\IarBuild.exe  %IAR_PRO_PATH% -build Debug

::type build_log.txt

echo Done.
::pause
 ```

