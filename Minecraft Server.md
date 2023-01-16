# Minecraft Server

注：此处不考虑内网穿透，请确保有公网IPv4或去买VPS

## 安装Ubuntu
这里以Ubuntu20.04为例（docker部署见后文）
本地服务器可参照[硬件茶谈](https://www.bilibili.com/video/BV1DJ411D79y)的装系统教程安装Ubuntu，如果你还没有划走，建议安装GUI版本（以下内容以远程命令行为示例，图形界面可打开终端进行操作，部分步骤直接使用图形界面会更方便）
云服务器直接选择对应镜像即可

## 开放防火墙
本地服务器请正确配置端口转发，详见路由器后台
云服务器请配置安全组，在安全组规则内添加25565端口的TCP进出站（如变更端口则开启对应端口防火墙，但不要给mc使用:22等端口）

## 配置Java
1.18及以上安装JDK17
1.17官方要求Java16，但建议JDK17（未测试）
1.12（17w13a）至Java版1.16.5（1.17：21w18a）需要Java 8启动游戏，但也建议使用更新版本Java

如果遇到问题，请先运行`sudo apt update`以更新apt

如果未安装Java，可以运行`java -version`以查看类似于以下内容，如已安装则会返回版本号：
```
sudo apt install default-jre              # version 2:1.11-72build1, or
sudo apt install openjdk-11-jre-headless  # version 11.0.14+9-0ubuntu2
sudo apt install openjdk-17-jre-headless  # version 17.0.2+8-1
sudo apt install openjdk-18-jre-headless  # version 18~36ea-1
sudo apt install openjdk-8-jre-headless   # version 8u312-b07-0ubuntu1
```
安装JDK8:
`sudo apt install openjdk-8-jdk`
安装JDK11:
`sudo apt install openjdk-11-jdk`
安装JDK17:
`sudo apt install openjdk-17-jdk`

通常情况下，安装新版本会更改默认Java为新版本，安装过程中可能需要输入`y`以继续
完成后通过`java -version`确认Java是否安装

## 安装Minecraft
使用`pwd`查看当前工作目录以确保现在的工作目录在/root下，这是你当前账户目录（对于使用VPS的人来说通常为此种情况），如果不是以root账户登录，则一般为/home（一般来说也只要没有乱来此条不会出问题，此目录可用`~/`表示）
当然，也可以将mc文件存在其他文件夹中，例如将文件存在/home/Minecraft中，可以用`mkdir /home/Minecraft`新建文件夹，然后`cd /home/Minecraft`进入到该目录，进行后续操作

可以参照https://www.minecraft.net/en-us/download/server 下载并启动最新版本Minecraft
如果你想查找先前版本，输入https://www.minecraft.net/en-us/article/minecraft-java-edition-1-18-1 到网址栏，其中1-18-1为版本号，您可以将其替换为1-16-5以查看1.16.5版本的下载链接(需要注意，1.12.2的release页面网址为https://www.minecraft.net/en-us/article/minecraft-1122-released，建议使用浏览器搜索对应版本的release页面并获取server.jar的下载链接)
>1.19.3:
>>https://piston-data.mojang.com/v1/objects/c9df48efed58511cdd0213c56b9013a7b5c9ac1f/server.jar
>
>1.18.2:
>>https://launcher.mojang.com/v1/objects/c8f83c5655308435b3dcf03c06d9fe8740a77469/server.jar
>
>1.18.1:
>>https://launcher.mojang.com/v1/objects/125e5adf40c659fd3bce3e66e67a16bb49ecc1b9/server.jar
>
>1.16.5
>>https://launcher.mojang.com/v1/objects/a16d67e5807f57fc4e550299cf20226194497dc2/server.jar
>
>1.14.3
>>https://launcher.mojang.com/v1/objects/d0d0fe2b1dc6ab4c65554cb734270872b72dadd6/server.jar
>
>1.14.2
>>https://launcher.mojang.com/v1/objects/808be3869e2ca6b62378f9f4b33c946621620019/server.jar
>
>1.13.1
>>https://launcher.mojang.com/mc/game/1.13.1/server/fe123682e9cb30031eae351764f653500b7396c9/server.jar
>
>1.12.2
>>https://launcher.mojang.com/mc/game/1.12.2/server/886945bfb2b978778c3a0288fd7fab09d315b25f/server.jar

您可以使用以上链接或从release页面中找到“Minecraft server jar”并复制链接，然后将链接复制到以下命令后方
`sudo wget `
>例子：`sudo wget https://launcher.mojang.com/v1/objects/a16d67e5807f57fc4e550299cf20226194497dc2/server.jar`
>此时应该能发现服务器开始下载server.jar文件，请确保当前目录中没有同名文件，否则要通过`mv`或其他命令更高文件名

请时刻注意命令行上的内容与提示，可能会出现[y/n]以询问是否继续

当文件下载完成后，可以通过`ls`命令查看当前目录下是否有server.jar文件出现，文件出现即可下一步。非从Microsoft下载的原版服务器可能需要解压缩，需要使用`unzip FileName.zip`命令解压，FilenName应当更改为下载文件的实际文件名，其他压缩包格式的解压方式见下
>`tar xvf FileName.tar`
>`gunzip FileName.gz`
>`gzip -d FileName.gz`
>`tar zxvf FileName.tar.gz`
>`tar zxvf FileName.tgz`
>`rar x FileName.rar`

## 同意EULA
输入`sudo java -Xms1920m -Xmx3840m -jar server.jar nogui`来首次启动，此时会报错并要求同意eula.txt（“-Xms1920m”中的1920m为启动内存，可以与后者数值相等但不可更大；“-Xmx3840m”中的3840m为程序最大内存，单位都为MB，最大内存不能超过实例/服务器物理内存，如果只运行mc，建议为可用内存的80%）
输入`vi eula.txt`来打开eula文件，此时及之后的工作目录都应保持在刚刚保存server.jar的目录(文件夹内)
按i键启用编辑，用键盘上的上下左右键将光标移到“eula=false”后方，删去false并改为“true”，确认文件内容为“eula=true”(上方注释不要管)，此操作即代表您同意[Minecraft的许可协议](https://account.mojang.com/documents/minecraft_eula)，然后按esc键退出编辑，并输入`:wq`来保存并退出（确认冒号为英文半角符号，控制台中输入的内容中一般不会包含中文及符号）

## 启动Minecraft
运行`sudo java -Xms4096m -Xmx6144m -jar server.jar nogui`以启动mc，**应当根据服务器规格更改此命令中的内存设置**，初次启动时间较长，如出现 OOT错误（out of memory 内存溢出）应考虑增加内存或降低Xmx后的数值，应至少保障机器有4G以上内存，内存要求根据需要不同有所变化。
启动完成后输入/help来查看是否正常运行（当然可以直接尝试连接，服务器ip请查看实例ip或你家的公网ip，端口不是25565需在ip后增加端口，如127.0.0.1:1050）
输入`/stop`以停止，输入`/op uesrname`给予用户op权限
如果不想让mc服务占用命令行前台，可以使用`nohup java -Xms3840M -Xmx5760M -jar server.jar nogui`启动服务（应当先关闭现有的mc服务器）

## 上传本地地图&更改世界地图

### 本地服务器
如果为本地服务器，可以通过插U盘然后运行`mv`命令将文件拷贝至mc目录下(noGUI，可能要`mnt`挂载u盘)，或直接拖拽复制/smb共享/云盘下载...(GUI)
然后更改level-name后的名称(world)为存档的文件夹名

### 云服务器
如果为云服务器，推荐使用S3上传文件（即对象存储，阿里云叫OSS，腾讯云叫COS，华为云叫OBS），当然如果有其他服务器，也可用http等方式下载，此处不做介绍

#### 阿里云创建Bucket
**阿里云的管理界面可能发生变动，请以[官方教程](https://help.aliyun.com/product/31815.html)为准，不当操作会导致意外损失，请自行承担责任**

以阿里云为例，打开[对象存储控制台](https://oss.console.aliyun.com/overview)，点击左侧“Bucket列表”菜单，然后点击创建Bucket，自行填写名称(需与其他Bucket名称不同，注意要符合名称规范)，地域要选与服务器所在地相同(不同会比较烧钱)，其他可以保持默认

#### 上传文件
创建完成后点击Bucket名进入，在文件列表中上传文档文件夹（更推荐用客户端上传，在下方“常用工具与服务”选项卡中查看），上传完成后可以看到文件夹出现在文件列表中

#### Accesskey
将光标移到右上角头像出，在弹出菜单中点击“Accesskey管理”，然后创建Accesskey，可能需要二步验证。请注意，在当前账号下有其他服务的情况下，应当采取妥善的安全措施。创建完成后请妥善保存Accesskey，如果secret遗失可以重新创建一个。请注意，AccessKey ID 和 AccessKey Secret 是您访问阿里云 API 的密钥，具有该账户完全的权限，请您妥善保管，被盗会造成严重损失。

#### 服务器安装ossutil

##### 运行以下命令下载ossutil
`wget https://gosspublic.alicdn.com/ossutil/1.7.14/ossutil64`

##### 运行以下命令修改文件执行权限。
`chmod 755 ossutil64`

##### 输入配置命令。
`./ossutil64 config`
然后直接按回车使用默认配置文件的路径。（默认为：/home/user/.ossutilconfig）

##### 根据提示设置工具的语言。
请输入语言CH或EN。工具使用的语言默认与操作系统保持一致。该配置项将在此次config命令设置成功后生效。

##### 根据提示分别设置Endpoint、AccessKey ID、AccessKey Secret和STSToken参数
Endpoint：填写Bucket所在地域的Endpoint。关于各地域Endpoint的更多信息，请参见[访问域名和数据中心](https://help.aliyun.com/document_detail/31837.htm?spm=a2c4g.11186623.0.0.80b62173DJaElH#concept-zt4-cvy-5db)。以北京的服务器和OSS为例，可填入`oss-cn-beijing-internal.aliyuncs.com`
accessKeyID和accessKeySecret请分别填入刚刚创建的ID与Secret
stsToken一般情况不填

##### 运行以下命令，验证是否已成功安装ossutil。
`./ossutil64`

#### 用ossutil下载存档
`./ossutil64 cp -r oss://examplebucket/destfolder/ localfolder_worldname/`
将“examplebucket”改为创建的bucket名称，“destfolder”改为上传的存档文件夹名
注意，应当先在服务器mc目录下运行`mkdir localfolder_worldname/`创建存档文件夹，建议“localfolder_worldname”与上传的文件夹名“destfolder”一致，然后执行上方命令
运行`ls`，查看新增的存档文件夹，然后运行`vi server.properties`，按i键启用编辑，更改"level-name="后的world为刚刚下载的存档文件夹名，然后按esc并输入`:wq`退出，重启服务器然后运行mc


## server.properties
在储存Minecraft文件的文件夹内运行`vi server.properties`查看配置文件
可以看到如下内容：
>#Minecraft server properties
>#[创建日期]
>enable-jmx-monitoring=false
>rcon.port=25575
>level-seed=
>gamemode=survival
>enable-command-block=false
>enable-query=false
>generator-settings={}
>level-name=world
>motd=A Minecraft Server
>query.port=25565
>pvp=true
>generate-structures=true
>difficulty=easy
>network-compression-threshold=256
>max-tick-time=60000
>require-resource-pack=false
>use-native-transport=true
>max-players=5
>online-mode=false
>enable-status=true
>allow-flight=false
>broadcast-rcon-to-ops=true
>view-distance=10
>server-ip=
>resource-pack-prompt=
>allow-nether=true
>server-port=25565
>enable-rcon=false
>sync-chunk-writes=true
>op-permission-level=4
>prevent-proxy-connections=false
>hide-online-players=false
>resource-pack=
>entity-broadcast-range-percentage=100
>simulation-distance=10
>rcon.password=
>player-idle-timeout=0
>force-gamemode=false
>rate-limit=0
>hardcore=false
>white-list=false
>broadcast-console-to-ops=true
>spawn-npcs=true
>spawn-animals=true
>function-permission-level=2
>level-type=default
>text-filtering-config=
>spawn-monsters=true
>enforce-whitelist=false
>resource-pack-sha1=
>spawn-protection=16
>max-world-size=29999984                                                     

下面介绍几个常用项
| 变量名      | 默认值   | 注释                                                         |
| ----------- | -------- | ------------------------------------------------------------ |
| level-seed  | [空]     | 设定创建世界的种子号，要在初次载入前更改，如果已经生成名为“world”(level-name的变量值，此处为默认名)的存档文件夹，删除文件夹后会重新创建此种子世界，或更改“level-name”以创建另一存档。此值可以为空，为空时生成随机种子 |
| gamemode    | survival | 创建世界所用的默认游戏模式，新进入玩家会以此模式开始游戏，值必须为以下四项中的一个：(adventure\|creative\|survival) |
| level-name  | world    | 存档名，存档为当前文件夹内的同名文件夹                       |
| pvp         | true     | 允许玩家间伤害，需为布尔值                                   |
| difficulty  | easy     | 游戏难度,值必须为以下四项中的一个单词或对应数字：peaceful (0)\|easy (1)\|normal (2)\|hard (3) |
| max-players | 20       | 允许的最大在线玩家数                                         |
| server-port | 25565    | MC服务器端口，如更改请开放对应防火墙，和ip共同构成服务器地址 |
| level-type  | default  | 默认即为minecraft:normal，也就是通常的游戏世界，改为minecraft:flat可生成超平坦 |

更多可以参考https://minecraft.fandom.com/wiki/Server.properties

未完待续...

可以参看此教程：https://minecraft.fandom.com/wiki/Tutorials/Setting_up_a_server
