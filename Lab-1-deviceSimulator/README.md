
## Device Registry

You should create resource on AWS console.

*  Register a Device in the Thing Registry

In this demo, your must create Thing that name is *"raspberry<XX>"*

[http://docs.aws.amazon.com/iot/latest/developerguide/register-device.html](http://docs.aws.amazon.com/iot/latest/developerguide/register-device.html)

* Create and Activate a Device Certificate and Active it.

[http://docs.aws.amazon.com/iot/latest/developerguide/create-device-certificate.html](http://docs.aws.amazon.com/iot/latest/developerguide/create-device-certificate.html)
> download all certificates, some certificates won't be allow to download after this page close.


* Create an AWS IoT Policy

[http://docs.aws.amazon.com/iot/latest/developerguide/create-iot-policy.html](http://docs.aws.amazon.com/iot/latest/developerguide/create-iot-policy.html)

* Attach an AWS IoT Policy to a Device Certificate

[http://docs.aws.amazon.com/iot/latest/developerguide/attach-policy-to-certificate.html](http://docs.aws.amazon.com/iot/latest/developerguide/attach-policy-to-certificate.html)

* Attach a Certificate to a Thing

[http://docs.aws.amazon.com/iot/latest/developerguide/attach-cert-thing.html](http://docs.aws.amazon.com/iot/latest/developerguide/attach-policy-to-certificate.html)

完成这一节后，您会在本地cert 文件中看到三个文件(certificate.pem, private.pem.key和rootCA.cert)，这三个文件将用于后续连接到AWS IoT平台。此外，还可以在AWS IoT 控制台看到您所创建的证书，安全策略，设备影子。AWS 为不同需求的客户提供了不同的解决方案，在实际生产中，您可以选用熟悉的编程语言SDK，根据自己的情况使用不同的设备注册方式。

Optionally you can put your cert file in the same directory: ~/cert

## Configure Your Raspberry Pi

#### 把传感器和二极管接入到树莓派

* 首先介绍一下DHT11：

DHT11是一个温湿度传感器，分为3个接口，分别为：VCC（+ 正极）, DATA, GND（- 负极）

VCC | 电源| +级，输入3V-5.5V|

DATA | 数据输出| 输出引脚|

GND | 接地 | -级别，接地引脚|


DHT11接线说明

1. VCC接上3V3，可以选择1口或者17口
2. DATA接上GPIO口，我选的是**GPIO2**，第3口
3. GND接上接地口，我选的是第14口

![DHT11-GPIO2](./images/DHT11-GPIO2.png)

把二极管接入到**GPIO3**

![GPIO3.png](./images/GPIO3.png)

请注意二极管分为正负极，长的一端为正极，短的一端为负极。

### Copy Cert file to raspberry pi 

After the Raspberry Pi system has been burned, connect the raspberry to the network, find the Raspberry Pi IP address, and log in to the Raspberry Pi using VNC or SSH.

Raspberry Pi default SSH account password:

**Username：pi**

**Password：raspberry**

Now you can use rsync to move your cert file to your raspberry pi.

```
rsync -r ~/cert/ pi@<your_ip_address>:~/
```

After login to Raspberry Pi, please install Git

```
sudo apt-get install git
```

Download the device code：

```
cd ~
rm -r aws-iot-labs (option)
git clone https://github.com/cncoder/aws-iot-labs.git
```

Copy cert file to the aws-iot-labs/Lab-1-deviceSimulator directory

```
mv ~/cert ~/aws-iot-labs/Lab-1-deviceSimulator
```

### 🚨⚠️🥁🎺 ULTRA SUPER IMPORTANT SPECIAL NOTE PLEASE READ THIS 🎺🥁⚠️🚨 ###

Make sure that every time you enter the same thing name (Thing Name) (raspberry<XX>)


## 把树莓派接入到AWS IoT

完成以上设备注册操作之后，就可以使用MQTT 连接到AWS IoT了。之后就可以远程看到本地传感器设备的状态了，甚至通过web 界面控制二极管开关。

首先切换到 aws-iot-raspberrypi 目录

```
cd ..
```

安装AWS IoT SDK

```
sudo pip install AWSIoTPythonSDK
```

修改连接文件配置，我们通过vim 来编辑这个文件

```
vi ThingShadowAgent.py
```

endpoint：登录您的 AWS IoT 控制台(https://console.amazonaws.cn/iot/home?region=cn-north-1#/settings)， 选择左边的设置，页面中的终端节点就是endpoint，对于每个AWS 账户这是唯一的。

thingName：**raspberry<组号>（上述注册设置的组名）**

topic：”**raspberry<组号>/sensor/data”**

运行连接文件

```
python ThingShadowAgent.py
```

此时您可以看到树莓排有大量输出，并提示已经连接成功，发送MQTT数据了。

现在我们可以通过AWS IoT 控制台，到事物影子查看树莓派上传到AWS IoT平台数据 。

1. 打开AWS IoT 控制台
2. 选择左边管理 => 事物
3. 点击您在上一节中创建的事物影子




