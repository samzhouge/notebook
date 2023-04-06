## vmware安装centos7 （参考《Linux就该这么学》）
* 创建新的虚拟机
* 典型
* 稍后安装操作系统
* 选择客户操作系统：Linux CentOS7 64 位
* 虚拟机名：CentOS7.6
* 自定义硬件
  * 新 CD/DVD选择ISO文件
  * 内存：2G
  * CPU：2
  * 网络适配器：NAT
  * 删除：USB/声卡/打印机设备等不需要要的设备
  ![](./images/新建虚拟机自定义硬件.png)
* 开始安装
  * 按Tab键，修改网卡显示名eth0，在最下面quiet后面追加 （参考《跟老男孩学Linux运维》）
    * net.ifnames=0 biosdevname=0
  * 最小化安装
  * root CentOS7!@#

## 克隆一个新虚拟机
* 设置mac地址，网卡适配器-网卡高级选项（在最下面，展开），生成MAC。不配置会每次也会自动生成。
