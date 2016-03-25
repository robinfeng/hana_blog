# SBO 9.2 HANA 安装笔记

---

## 下载安装文件

* 下载 SUSE Linux Enterprise 11 64 bit
用浏览器访问 https://www.suse.com/slesb1hana, 下载 SUSE Linux Enterprise 11 64 bit 安装镜像 ISO 文件 (SLES11_SP3_B1.x86_64–1.0.31.preload.iso). 

* 从 [SAP ONE Support Launchpad](https://launchpad.support.sap.com/#/softwarecenter), 下载 SAP 9.2 for HANA 及相关工具:
	- 安装与升级 > By Alphabetical Index (A-Z) > B > SAP B1 ANALYTICS ON SAP HANA > 下载 > SAP B1 ANALYTICS 1.2 ON HANA > 下载 > INSTALLTION: 51050558

	- 安装与升级 > By Alphabetical Index (A-Z) > B > SAP BUSINESS ONE PRODUCTS > SAP B1 VERSION FOR SAP HANA > 下载
		- SAP B1 9.2 for HANA > 下载
			- INSTALLTION: 51050324 (51050324_1, 51050324_2)
		- SAP B1 9.2 for HANA > 下载 > SAP HANA PLATFORM EDIT. 1.0 FOR B1 > 下载
			- SAP HANA AFL 1.0 FOR B1:  
HANA_AFL100_102P_50-70000758.SAR
			- SAP HANA CLIENT 1.0 FOR B1: 
				- LINUX SUSE ON X86_64 64BIT:  
HANA_CLIENT100_102_5-70000737.SAR
				- WINDOWS SERVER ON X64 64BIT:  
HANA_CLIENT100_102_5-70000783.SAR
			- SAP HANA DATABASE 1.0 FOR B1:  
HANA_SERVER100_102_5-70000738.SAR
			- SAP HANA STUDIO 1.0 FOR B1:
				- LINUX SUSE ON X86_64 64BIT:  
HANA_STUDIO100_102_5-70000739.SAR
				- WINDOWS SERVER ON IA32 32BIT:  
HANA_STUDIO100_102_5-70000781.SAR
				- WINDOWS ON X64 64BIT:  
HANA_STUDIO100_102_5-70000740.SAR

	- 支持包与补丁 > By Alphabetical Index (A-Z) > SAPCAR > SAPCAR 7.21
		- LINUX ON X86_64 64BIT:  
SAPCAR_617-80000935.EXE
		- WINDOWS SERVER ON IA32 32BIT:  
SAPCAR_617-80000936.EXE
		- WINDOWS ON X64 64BIT:  
SAPCAR_617-80000938.EXE

说明: 可以根据你所安装的环境, 自行下载相应的版本.

---

## 安装 VMware 

VMware Workstation 12 Pro, 并为 SUSE 虚拟机设置:

| | |
|---|---|
|CPU| 8|
|内存| 28GB (不低于 24GB)|
|硬盘| 300GB|
|网络链接| 桥接模式|

---

## 安装 SUSE Linux Enterprise 11 64 bit

使用 ISO 映像文件 **SLES11_SP3_B1.x86_64-1.0.31.preload.iso**

### 网络设置

|  |  |
|---|---|
|Hostname| hana-demo1|
|Domain Name| tech-sonic.net|
|Name Server 1| 202.96.209.133|
|Default Gateway| 192.168.1.8|
|Statically assigned IP Address| 192.168.1.44|
|Subnet Mask| 255.255.255.0|
|Hostname| hana-demo1.tech-sonic.net|

### SAP HANA 分区

| | |
|---|---|
|/usr/sap/|50GB|
|/hana/shared/|1xRAM, 32GB|
|/hana/data/|3xRAM, 96GB|
|/hana/log/|1xRAM, 32GB|

### Samba for SUSE 安装

为了便于传输安装文件, 建议在 SUSE 系统安装 Samba Server

路径: Computer > YaST > Samba Server

| | |
|---|---|
|Share Name|HANA_DATA_UNITS|
|Share Type|Directory|
|Share Path|/hana/DATA_UNITS|
|Read Only|No|

路径: Computer > YaST > System Services (Runlevel)
找到 smb, 点击 Enable

### HANA for SUSE 安装

* 将下载文件复制到 SUSE 的 /hana/DATA_UNITS 目录中, 如下:

```
- /DATA_UNITS
  - /SAPCAR_617-80000935.EXE
  - /HANA_SERVER100_102_5-70000738.SAR
  - /HANA_AFL100_102P_50-70000758.SAR
  - /HANA_CLIENT100_102_5-70000737.SAR
  - /Product_920.003.00_CD_1442273_HANA.rar
```

* 用 SAPCAR 将以上 SAR 文件解压

```
hana-demo1:/hana/DATA_UNITS # chmod 755 SAPCAR_617-80000935.EXE
hana-demo1:/hana/DATA_UNITS # ./SAPCAR_617-80000935.EXE -manifest SIGNATURE.SMF -xvf HANA_SERVER100_102_5-70000738.SAR
hana-demo1:/hana/DATA_UNITS # ./SAPCAR_617-80000935.EXE -manifest SIGNATURE.SMF -xvf HANA_AFL100_102P_50-70000758.SAR
hana-demo1:/hana/DATA_UNITS # ./SAPCAR_617-80000935.EXE -manifest SIGNATURE.SMF -xvf HANA_CLIENT100_102_5-70000737.SAR
```

* 将 Product_920.003.00_CD_1442273_HANA.rar 解压

* 目录结构如下:

```
- /DATA_UNITS
  - /SAP_HANA_AFL
  - /SAP_HANA_DATABASE
  - /SAP_HANA_CLIENT
  - /SAP_HANA_STUDIO
  - /Product_920.003.00_CD_1442273_HANA
```

### 在 SUSE 上安装 SAP HANA PLATFORM EDIT.1.0 FOR B1

* 1 安装 SAP_HANA_DATABASE

```
hana-demo1:/hana/DATA_UNITS # cd SAP_HANA_DATABASE
hana-demo1:/hana/DATA_UNITS/SAP_HANA_DATABASE # chmod +x hdbsetup
hana-demo1:/hana/DATA_UNITS/SAP_HANA_DATABASE # chmod +x ./instruntime/sdbrun
hana-demo1:/hana/DATA_UNITS/SAP_HANA_DATABASE # ./hdbsetup
```

SID: DB1
Instance Number: 00
Instance Directory: /usr/sap/DB1/HDB00
Version: 1.00.102.05.1455873859

* 2 安装 SAP_HANA_AFL

```
hana-demo1:/hana/DATA_UNITS # cd SAP_HANA_AFL
hana-demo1:/hana/DATA_UNITS/SAP_HANA_AFL # chmod +x hdbinst
hana-demo1:/hana/DATA_UNITS/SAP_HANA_AFL # chmod +x ./instruntime/sdbrun
hana-demo1:/hana/DATA_UNITS/SAP_HANA_AFL # ./hdbinst
```

* 3 安装 SAP_HANA_CLIENT

```
hana-demo1:/hana/DATA_UNITS # cd SAP_HANA_CLIENT
hana-demo1:/hana/DATA_UNITS/SAP_HANA_CLIENT # chmod +x hdbsetup
hana-demo1:/hana/DATA_UNITS/SAP_HANA_CLIENT # chmod +x ./instruntime/sdbrun
hana-demo1:/hana/DATA_UNITS/SAP_HANA_CLIENT # ./hdbsetup
```

* 4 安装 Product_920.003.00_CD_1442273_HANA

```
hana-demo1:/hana/DATA_UNITS # cd Product_920.003.00_CD_1442273_HANA
hana-demo1:/hana/DATA_UNITS/Product_920.003.00_CD_1442273_HANA/Packages.Linux/ServerComponents # chmod +x install
hana-demo1:/hana/DATA_UNITS/Product_920.003.00_CD_1442273_HANA/Packages.Linux/ServerComponents # ./install

```
Database server for SLD: 
SAP HANA Server: localhost
Port: 30115
SLD Schema name: SLDData

Warning: The script server is not running. Start the script server before proceeding (see the Administrator's Guid for details)

* 5 安装 Studio

```
hana-demo1:/hana/DATA_UNITS # cd SAP_HANA_STUDIO
hana-demo1:/hana/DATA_UNITS/SAP_HANA_STUDIO # chmod +x hdbsetup
hana-demo1:/hana/DATA_UNITS/SAP_HANA_STUDIO # chmod +x ./instruntime/sdbrun
hana-demo1:/hana/DATA_UNITS/SAP_HANA_STUDIO # ./hdbsetup
```

* 可选: 手工启动 HANA 服务

```
# su - db1adm
hana-demo1:/usr/sap/DB1/HDB00> HDB start
```

---

## 在 Windows Server 上, 安装 SAP B1 9.2 for HANA

将 51050324 解压缩后, 再将 Product_920.003.00_CD_1442273_HANA.rar 解压至 F:\B1_9.2_onHANA\51050324

* JDK 1.6 / 1.7
http://www.oracle.com/technetwork/java/javase/archive-139210.html

* HANA Client (32 bit/ 64 bit)
注意: 32 位和 64 位两个版本都要安装

* HANA Studio

* SAP B1 Client for HANA

访问以下两个路径安装 32位和 64位 B1 客户端:
\\192.168.1.44\b1_shf\Client, \\192.168.1.44\b1_shf\Client.x64

* 安装 安装 SAP B1 9.2 for HANA
F:\B1_9.2_onHANA\51050324\Setup.exe

---

## SAP HANA 许可证

* 删除当前的临时 HANA 许可证
SAP HANA Studio > DB1 > 右键菜单 > Properties >> License
点击 Delete License Key

* 安装正式的永久 HANA 许可证
SAP HANA Studio > DB1 > 右键菜单 > Properties >> License
点击 Install License Key

* 重新启动 HANA 服务
SAP HANA Studio > DB1 > 右键菜单 > Stop...
SAP HANA Studio > DB1 > 右键菜单 > Start...

## SAP B1 许可证

打开浏览器访问 https://192.168.1.44:40000/LicenseControCenter/ (大小写敏感)

---

## 遇到过的问题

* 无法连接许可证服务器，请输入有效密码
需要安装 32 位客户端: HANA_CLIENT100_102_5-70000782.SAR

* 登录 B1 后无法打开仪表盘, 并显示错误: Signature validation of SAML2Assertion failed.
重新启动 HANA 服务

* SUSE 服务器报错: The volume "Filesystem root" has only 0 bytes disk space remaining.
