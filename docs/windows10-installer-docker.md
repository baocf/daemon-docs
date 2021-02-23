Docker Desktop



#### WIn----程序于功能----更多的功能----启用或关闭 Windows功能--->Hyper-V--开启后重新启动



## windows启动Docker失败 An error occurred

以管理员方式打开CMD, 运行`netsh winsock reset` 后, 再次启动Docker就可以了

## 手动安装步骤Manual Installation Steps

如果你没有使用 Windows 预览体验版本，则需要按照以下步骤手动启用 WSL 所需的功能。If you are not on a Windows Insiders build, the features required for WSL will need to be enabled manually following the steps below.



https://docs.microsoft.com/zh-cn/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package

## 步骤 1 - 启用适用于 Linux 的 Windows 子系统Step 1 - Enable the Windows Subsystem for Linux

需要先启用“适用于 Linux 的 Windows 子系统”可选功能，然后才能在 Windows 上安装 Linux 分发。You must first enable the "Windows Subsystem for Linux" optional feature before installing any Linux distributions on Windows.

以管理员身份打开 PowerShell 并运行：Open PowerShell as Administrator and run:

PowerShell复制

```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

建议现在转到步骤 #2，更新到 WSL 2，但如果只想安装 WSL 1，现在可以重新启动计算机，然后继续执行[步骤 6 - 安装所选的 Linux 发行版](install-win10#step-6---install-your-linux-distribution-of-choice)。We recommend now moving on to step #2, updating to WSL 2, but if you wish to only install WSL 1, you can now **restart** your machine and move on to [Step 6 - Install your Linux distribution of choice](install-win10#step-6---install-your-linux-distribution-of-choice). 若要更新到 WSL 2，请等待重新启动计算机，然后继续执行下一步。To update to WSL 2, **wait to restart** your machine and move on to the next step.

## 步骤 2 - 检查运行 WSL 2 的要求Step 2 - Check requirements for running WSL 2

若要更新到 WSL 2，需要运行 Windows 10。To update to WSL 2, you must be running Windows 10.

- 对于 x64 系统：**版本 1903** 或更高版本，采用 **内部版本 18362** 或更高版本。For x64 systems: **Version 1903** or higher, with **Build 18362** or higher.
- 对于 ARM64 系统：**版本 2004** 或更高版本，采用 **内部版本 19041** 或更高版本。For ARM64 systems: **Version 2004** or higher, with **Build 19041** or higher.
- 低于 18362 的版本不支持 WSL 2。Builds lower than 18362 do not support WSL 2. 使用 [Windows Update 助手](https://www.microsoft.com/software-download/windows10)更新 Windows 版本。Use the [Windows Update Assistant](https://www.microsoft.com/software-download/windows10) to update your version of Windows.

若要检查 Windows 版本及内部版本号，选择 Windows 徽标键 + R，然后键入“winver”，选择“确定”。To check your version and build number, select **Windows logo key + R**, type **winver**, select **OK**. （或者在 Windows 命令提示符下输入 `ver` 命令）。(Or enter the `ver` command in Windows Command Prompt). 更新到“设置”菜单中的[最新 Windows 版本](ms-settings:windowsupdate)。[Update to the latest Windows version](ms-settings:windowsupdate) in the Settings menu.

 备注

如果运行的是 Windows 10 版本1903 或 1909，请在 Windows 菜单中打开“设置”，导航到“更新和安全性”，然后选择“检查更新”。If you are running Windows 10 version 1903 or 1909, open "Settings" from your Windows menu, navigate to "Update & Security" and select "Check for Updates". 内部版本号必须是 18362.1049+ 或 18363.1049+，次要内部版本号需要高于 .1049。Your Build number must be 18362.1049+ or 18363.1049+, with the minor build # over .1049. 阅读详细信息：[WSL 2 即将支持 Windows 10 版本 1903 和 1909](https://devblogs.microsoft.com/commandline/wsl-2-support-is-coming-to-windows-10-versions-1903-and-1909/)。Read more: [WSL 2 Support is coming to Windows 10 Versions 1903 and 1909](https://devblogs.microsoft.com/commandline/wsl-2-support-is-coming-to-windows-10-versions-1903-and-1909/). 请参阅[疑难解答说明](troubleshooting#im-on-windows-10-version-1903-and-i-still-do-not-see-options-for-wsl-2)。See the [troubleshooting instructions](troubleshooting#im-on-windows-10-version-1903-and-i-still-do-not-see-options-for-wsl-2).

## 步骤 3 - 启用虚拟机功能Step 3 - Enable Virtual Machine feature

安装 WSL 2 之前，必须启用“虚拟机平台”可选功能。Before installing WSL 2, you must enable the **Virtual Machine Platform** optional feature.

以管理员身份打开 PowerShell 并运行：Open PowerShell as Administrator and run:

PowerShell复制

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

**重新启动** 计算机，以完成 WSL 安装并更新到 WSL 2。**Restart** your machine to complete the WSL install and update to WSL 2.

## 步骤 4 - 下载 Linux 内核更新包Step 4 - Download the Linux kernel update package

1. 下载最新包：Download the latest package:

   - [适用于 x64 计算机的 WSL2 Linux 内核更新包WSL2 Linux kernel update package for x64 machines](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

    备注

   如果使用的是 ARM64 计算机，请下载 [ARM64 包](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_arm64.msi)。If you're using an ARM64 machine, please download the [ARM64 package](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_arm64.msi) instead. 如果不确定自己计算机的类型，请打开命令提示符或 PowerShell，并输入：`systeminfo | find "System Type"`。If you're not sure what kind of machine you have, open Command Prompt or PowerShell and enter: `systeminfo | find "System Type"`.

2. 运行上一步中下载的更新包。Run the update package downloaded in the previous step. （双击以运行 - 系统将提示你提供提升的权限，选择“是”以批准此安装。）(Double-click to run - you will be prompted for elevated permissions, select ‘yes’ to approve this installation.)

安装完成后，请继续执行下一步 - 在安装新的 Linux 分发时，将 WSL 2 设置为默认版本。Once the installation is complete, move on to the next step - setting WSL 2 as your default version when installing new Linux distributions. （如果希望将新的 Linux 安装设置为 WSL 1，请跳过此步骤。）(Skip this step if you want your new Linux installs to be set to WSL 1).

 备注

有关详细信息，请参阅 [Windows 命令行博客](https://aka.ms/cliblog)上的文章[对更新 WSL2 Linux 内核的更改](https://devblogs.microsoft.com/commandline/wsl2-will-be-generally-available-in-windows-10-version-2004)。For more information, read the article [changes to updating the WSL2 Linux kernel](https://devblogs.microsoft.com/commandline/wsl2-will-be-generally-available-in-windows-10-version-2004), available on the [Windows Command Line Blog](https://aka.ms/cliblog).

## 步骤 5 - 将 WSL 2 设置为默认版本Step 5 - Set WSL 2 as your default version

打开 PowerShell，然后在安装新的 Linux 发行版时运行以下命令，将 WSL 2 设置为默认版本：Open PowerShell and run this command to set WSL 2 as the default version when installing a new Linux distribution:

PowerShell复制

```powershell
wsl --set-default-version 2
```

## 步骤 6 - 安装所选的 Linux 分发Step 6 - Install your Linux distribution of choice

1. 打开 [Microsoft Store](https://aka.ms/wslstore)，并选择你偏好的 Linux 分发版。