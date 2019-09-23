#  win安装rebbitmq                                                                   
## 目录                                                                
- [下载/安装Erlang语言开发包](#下载/安装Erlang语言开发包)                                                        
- [下载/安装RabbitMQ](#下载/安装RabbitMQ) 
- 

#下载/安装Erlang语言开发包
下载地址 http://www.erlang.org/download.html 
图片: https://uploader.shimo.im/f/Jh42yvAXvhc22svq.png
配置环境变量 ERLANG_HOME D:\Program Files\erl9.2，添加到PATH  %ERLANG_HOME%\bin;

#下载/安装RabbitMQ 
 下载地址 http://www.rabbitmq.com/download.html 
 安装教程：http://www.rabbitmq.com/install-windows.html，
download rabbitmq-server-3.7.17.exe) and run it. It installs RabbitMQ as a Windows service and starts it using the default configuration.
配置环境变量 C:\RDBase\DevPlatform\RabbitMQ Server\rabbitmq_server-3.7.17，添加到PATH %RABBITMQ_SERVER%\sbin;
进入%RABBITMQ_SERVER%\sbin 目录以管理员身份运行 rabbitmq-plugins.bat，安装完成之后以管理员身份启动 rabbitmq-service.bat (事实上，安装完上述exe文件就已经启动了rabbit server

图片: https://uploader.shimo.im/f/ww9oqibhZ2oh8dyX.png


从windows service可以看到是启动了 AMQP, 端口5672
图片: https://uploader.shimo.im/f/rVAo6Lg6u84RSt14.png

图片: https://uploader.shimo.im/f/GdibRTsfpsAXAhJ1.png

可以通过命令: 
参考官网: https://www.rabbitmq.com/install-windows.html
rabbitmqctl status  状态
net stop/start/disable/enable RabbitMQ  服务关闭/启动  等效于
rabbitmq-service stop/start

15672   rabbitmq webUi
参考官网  https://www.rabbitmq.com/management.html
开启rabbitmq_management
rabbitmq-plugins enable rabbitmq_management
图片: https://uploader.shimo.im/f/gJg93rtHDSkFrNPj.png

127.0.0.1:15672的登录用户/密码: guest/guest
图片: https://uploader.shimo.im/f/dXMQoNgzC8IgNaZ6.png

4369 (epmd), 25672 (Erlang distribution)
5672, 5671 (AMQP 0-9-1 without and with TLS)
15672 (if management plugin is enabled)
61613, 61614 (if STOMP is enabled)
1883, 8883 (if MQTT is enabled)
 4369: epmd, a peer discovery service used by RabbitMQ nodes and CLI tools
5672, 5671: used by AMQP 0-9-1 and 1.0 clients without and with TLS
25672: used for inter-node and CLI tools communication (Erlang distribution server port) and is allocated from a dynamic range (limited to a single port by default, computed as AMQP port + 20000). Unless external connections on these ports are really necessary (e.g. the cluster uses federation or CLI tools are used on machines outside the subnet), these ports should not be publicly exposed. See networking guide for details.
35672-35682: used by CLI tools (Erlang distribution client ports) for communication with nodes and is allocated from a dynamic range (computed as server distribution port + 10000 through server distribution port + 10010). See networking guide for details.
15672: HTTP API clients, management UI and rabbitmqadmin (only if the management plugin is enabled)
61613, 61614: STOMP clients without and with TLS (only if the STOMP plugin is enabled)
1883, 8883: (MQTT clients without and with TLS, if the MQTT plugin is enabled
15674: STOMP-over-WebSockets clients (only if the Web STOMP plugin is enabled)
15675: MQTT-over-WebSockets clients (only if the Web MQTT plugin is enabled)



