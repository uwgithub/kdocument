#chocolatey包管理器                                                                   
## 目录  
- [Install Chocolatey](#InstallChocolatey)    
- [UnInstall Chocolatey](#UnInstallChocolatey) 
- [Install Software](#InstallSoftware) 
- [Need Update Software List](#NeedUpdateSoftwareList) 
- [Update Software](#UpdateSoftware) 
- [List Installed Software](#ListInstalledSoftware)
- [Uninstall Software](#UninstallSoftware)
- [Search Software](#SearchSoftware)
- [View Software Information](#ViewSoftwareInformation)
- [Temp Path](#Temp Path)
      

#设置choco安装目录
   最新版本的 chocolatey 的默认安装路径是：

     C:\ProgramData\Chocolatey
  如何修改为自己的路径呢？
  在 系统环境变量 中增加 ChocolateyInstall：

    C:\RDBase\Utilities\OS\Chocolatey
  替换成你自己的路径

   ![avatar][chocoenvbase64]
 
  然后打开 cmd 窗口，执行如下命令：


    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "(iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))) >$null 2>&1" && SET PATH=%PATH%;%ChocolateyInstall%\bin
 

  安装完成之后，chocolatey 会自动将路径添加到 用户环境变量 中。


#InstallChocolatey
CMD (Recommand):

    @"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "(iex ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))) >$null 2>&1" && SET PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin
POWERSHELL:

    Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))

#UnInstallChocolatey
  直接删除文件夹和环境变量即可 
  C:\Users\Administrator\AppData\Local\Temp\chocolatey 

#Install Software
    
    --install-directory=value    在安装命令行加入这个参数 , 修改默认安装软件默认安装路径
      比如  --install-directory ="D:\Soft"
    set http_proxy=http://10.192.124.220:80
    set https_proxy=http://10.192.124.220:80
    set no_proxy=127.0.0.1,::1,localhost
    
    choco install -y notepad2-mod 7zip GoogleChrome git gpg4win pandoc putty typora wkhtmltopdf vscode
    choco install -y sqlite sqlitebrowser cmder firefox nodejs sourcetree heidisql
    choco install -y axel jq telnet caffeine sudo
    choco install -y keepass Everything listary IrfanView irfanviewplugins mpv youtube-dl foobar2000 sumatrapdf 
    choco install -y cpu-z hashmyfiles procexp autohotkey
    choco install -y --ignore-checksums autoruns
    choco install -y google-pinyin ilspy intellijidea-community slack
    choco pin add -n=intellijidea-community
    (choco pin add -n=git --version 1.2.3)
    
    choco install -y golang
    choco pin add -n=golang
    (choco pin remove --name golang)
    
    (--force argument to force reinstall, don't use it on choco upgrade command)
    choco install -y --force notepad2-mod

#Need Update Software List
    choco outdated


#Update Software
    choco upgrade all
    choco upgrade chocolatey

  No Update Some Software When Upgrade:

    choco pin notepad2-mod

#List Installed Software
    choco list --local-only

List Installed Software (Include Non-Chocolatey-Install) 

    choco list -li
    choco list -lai

#Uninstall Software
    choco uninstall sourcetree

#Search Software
    choco search docker-desktop
    choco search > 1.txt

#View Software Information
    choco info docker-desktop

#Temp Path
      %temp%\chocolatey





[chocoenvbase64]:data:image/png;base64,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