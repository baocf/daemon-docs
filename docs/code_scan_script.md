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







1. **PC 本地IDE的编译支持，License配置；**

2. **构建IDE对应的编译脚本；**

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

   

3. **codeDex插件的安装(coverity2019.03)；**

     base_package:   a. CodeDEX_V3;  b. jenkins master plugins for CodeDEX

     

     scan_tools:  Coverity_2019.03

   1. {CI安装目录}/plugin/CodeDex_v3/tool/tools/coverity_xxxx\bin；
   2. Coverity_xxx安装到指定的tools目录下；

4. **安装指定的编译器类型，IAR对应的iccarm.**

   cov_configure -lsct //查看Coverity工具支持的编译列表

   //IAR config

   cov_configure --template --compiler iccarm --comptype iar

   ```powershell
   set CI_ROOT=D:\FD-IAR\ci\plugins
   set COVERITY_HOME=%CI_ROOT%\CodeDEX_V3_Win64\CodeDEX_V3\tool\tools\coverity_2019.03
   set COVERITY_TOOL=%CI_ROOT%\CodeDEX_V3_Win64\CodeDEX_V3\tool\7za\Windows
   set PATH=%COVERITY_HOME%\bin；%PATH%
   set language=c
   
   set inner_dir=D:\FD-IAR\ci\temp_aw70
   set cov_tmp_dir=%inner_dir%\cov_tmp
   
   rmdir /q /s %inner_dir%
   
   call %COVERITY_HOME%\bin\cov_build.exe --dir %cov_tmp_dir% iar_build.bat
   
   cd /d %cov_tmp_dir%
   %COVERITY_TOOL%\7za.exe a -tzip coverity.zip * -r
   xcopy coverity.zip "%inner_dir%" /S /Q /Y /H /R /I
   ```

   coverity.zip解压后，目录里面有一个build-log.txt, 文件最后一行有显示扫描包的覆盖率

5. SecSolar project 的创建，产品线的配置， 获取到 project_name & register_key

6. 配置Jenkins  master job  task 创建。 添加slave node, 安装 coverity_phi插件，配置中间件及脚本， 报告的push规则；

7. 任务构建，确保build.log扫描的覆盖率， 是否是95%+

8. 完成代码扫描， 根据检测规则，进行代码扫描，处理解决或屏蔽漏洞；

9. 通过Secsonar平台生成export code  报告；

     

   

   

   

   

   

   - 涉及脚本

     coverity_build.bat

     - IDE:
   
       iar_build.bat
   
       ```powershell
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
   
       
   
     - plugin1:
   
       coverity.bat
   
       ```powershell
       @echo off
       set JAVA_HOME=D:\java\jdk1.8.0_162
       
       set CI_ROOT=D:\FD-IAR\ci\plugins
       set COVERITY_HOME=%CI_ROOT%\..\CodeDEX_V3\tool\tools\coverity_2019.03
       set COVERITY_TOOL=%CI_ROOT%\..\CodeDEX_V3\tool\7za\Windows
       set BINSCOPE_BIN=%CI_ROOT%\..\CodeDEX_V3\tool\tools\binscope_3.0.5
       set CODEMARS_BIN=%CI_ROOT%\..\CodeDEX_V3\tool\tools\codemars_Newest
       set SECMISSILE_BIN=%CI_ROOT%\..\CodeDEX_V3\tool\tools\secmissile
       set SOURCE_SECOND_BIN=D:\FD_IAR\..\app_v3\iar\bin\freertos_amota.bin
       set SOURCE_PATH=D:\FD_IAR\code\app_v3
       set PATH=%BINSCOPE_BIN%\bin;%CODEMARS_BIN%\bin;%SECMISSILE_BIN%\bin;%COVERITY_HOME%\bin；%PATH%
       set language=c
       
       set inner_dir=D:\FD-IAR\ci\temp_aw70
       set cov_tmp_dir=%inner_dir%\cov_tmp
       
       rmdir /q /s %inner_dir%
       
       rem #################20200624 coverity_test ok################
       call %COVERITY_HOME%\bin\cov_build.exe --dir %cov_tmp_dir% iar_build.bat
       
       cd /d %cov_tmp_dir%
       %COVERITY_TOOL%\7za.exe a -tzip coverity.zip * -r
       xcopy coverity.zip "%inner_dir%" /S /Q /Y /H /R /I
       
       rem #################20200701 binscope_test ok################
       rem type1
       %BINSCOPE_HOME%\binscope /d %SOURCE_SECOND_BIN%
       rem binscope.zip 与 binscope.bat在用一级目录
       move binscope.zip %inner_dir%
       
       rem type2
       call binscope.bat
       
       rem #################20200702 codemars_test ok################
       rem type1
       rem -c 指c语言代码  -source 源代码的路径  -output 输出的报告文件   -codedexPrj SecSolar Portal 创建产品线工程名 
       %CODEMARS_HOME%\CodeMars.bat  -c  -source  %SOURCE_PATH% -output  %inner_dir% -codedexPrj app_v3
       rem type2
       call codemars.bat
       
       ```
   
       
   
     - plugin2
   
       codemars.bat
   
       ```powershell
       @echo off
       set CODEMARS_HOME=..\..\coveDEX_v3\tool\tools\codemars_Newest
       set SOURCE_PATH=D:\FD_IAR\code\app_v3
       set PATH= %CODEMARS_HOME%:$PATH
       set inner_dir=D:\FD-IAR\temp_v3
       
       rem -c 指c语言代码  -source 源代码的路径  -output 输出的报告文件   -codedexPrj SecSolar Portal 创建产品线工程名 
       %CODEMARS_HOME%\CodeMars.bat  -c  -source  %SOURCE_PATH% -output  %inner_dir% -codedexPrj app_v3
       
       ```
   
       
   
       
   
     - plugin3
   
       binscope.bat
   
       ```powershell
       @echo off
       set BINSCOPE_HOME=..\..\coveDEX_v3\tool\tools\binscope_3.0.5
       set SOURCE_SECOND_BIN=D:\FD_IAR\code\app_v3\iar\bin\freertos_amota.bin
       set PATH= %BINSCOPE_HOME%:$PATH
       set inner_dir=D:\FD-IAR\temp_v3
       
       %BINSCOPE_HOME%\binscope /d %SOURCE_SECOND_BIN%
       
       rem binscope.zip 与 binscope.bat在用一级目录
       move binscope.zip %inner_dir%
       
       ```
   
       
   
       
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   
   