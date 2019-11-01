#安装node-npm                                                                  
## 目录                                                                
- [下载安装node](#下载安装node) 
- [安装前端项目](#安装前端项目) 




#下载安装node
   当前长期支持版: 10.16.3 (包含 npm 6.9.0) : [https://nodejs.org/zh-cn/download/](https://nodejs.org/zh-cn/download/ "node下载")
   Windows 安装包 (.msi) 64bit 下载 安装
   如果安装过程中出现，没有足够权限设置path,就手动设置，先通过命令行查看NODE_ENV是否存在;然后可视窗口设置。
    [https://www.cnblogs.com/fhen/p/6293763.html](https://www.cnblogs.com/fhen/p/6293763.html " 设置Node环境变量")

    --windows
    #node中常用的到的环境变量是NODE_ENV，首先查看是否存在
    set NODE_ENV
    #如果不存在则添加环境变量
    set NODE_ENV=C:\RDBase\DevPlatform\nodejs
    #环境变量追加值
    set 变量名=%变量名%;变量内容
    set path=%path%;%NODE_ENV%
    #某些时候需要删除环境变量
    set NODE_ENV=

    --linux
    #node中常用的到的环境变量是NODE_ENV，首先查看是否存在
    echo $NODE_ENV
    #如果不存在则添加环境变量
    export NODE_ENV=production
    #环境变量追加值
    export path=$path:/home/download:/usr/local/


    所有命令，基本都是在项目路径下，或者直接在idea的terminal里 
    //打开命令提示如测试安装是否成功
    node -v
    npm -v


#安装安装前端项目
    npm install -g xx-app yarn

    //查看全局安装路径
    npm root -g
    
   