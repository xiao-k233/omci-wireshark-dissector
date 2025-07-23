# OMCI Wireshark 解析器

## 简介

这是一个用于 Wireshark 的 ONT 管理与控制接口（OMCI）协议解析器，支持 ITU Rec. G984.4 和 G988 标准。

## 环境要求

- **Wireshark 版本**：1.4.3 或更高版本
- **Lua 版本**：至少需要 Lua 5.1

您可以通过 Wireshark 菜单中的 [帮助/关于 Wireshark][def] 查看当前版本信息。
![帮助/关于 Wireshark][def]

## 安装配置

1. 从 [GitHub](https://github.com/xiao-k233/omci-wireshark-dissector) 下载源代码
2. 将 `omci.lua` 文件复制到 Wireshark 插件目录
   - 对于Windows，插件目录通常为`%APPDATA%\Wireshark\plugins`(直接输入到文件资源管理器的地址栏中，如没有，可以新建)
   - 对于 Ubuntu、Debian、Fedora 和 Redhat 系统，插件目录通常为：`$HOME/.config/wireshark/plugins`

完成以上步骤后，插件即可正常使用。

## Lantiq/MaxLinear 芯片组教程

假设您的设备是WAS-110(或其他prx126方案PON Stick)，并使用我的8311固件Fork（最新版本）,您可以直接在ssh执行`8311-capture-omci-msg.sh`来进行OMCI抓包，可以通过拔下光纤并重新插上，强制进行一次 PON 认证，这会产生大量OMCI日志。注意，生成的hex文件**包含您LOID等敏感信息**，不要公开发布！！

假如您采用其他固件，或者MA5671A等老芯片，可以采用如下方式抓包：
1. `omci_pipe.sh mdfe /tmp/omci.log`开始抓包
2. 等待一会，期间可以`cat`一下`/tmp/omci.log`来看抓包多少了
3. `omci_pipe.sh mdd`停止抓包
4. `awk '/^rx\|/ || /^tx\|/ {gsub(/.*\|/, ""); print "000000 " $0}' "/tmp/omci.log" > "/tmp/omci.hex"`使其转换为hex文件
5. 使用`scp`回传
6. 在Wireshark中，`文件-从Hex转储导入`
7. 上面选择回传的hex文件，下方封装类型选择Ethernet,更下方点击以太网，填入以太类型（十六进制）`88b5`
8. 不会看图
![导入Hex文件步骤1](https://github.com/xiao-k233/omci-wireshark-dissector/raw/master/guide-one.png)
![导入Hex文件步骤2](https://github.com/xiao-k233/omci-wireshark-dissector/raw/master/guide-two.png)

## 使用示例

您可以使用我们提供的 [示例数据包](https://github.com/xiao-k233/omci-wireshark-dissector/blob/master/omci-example.pcap?raw=true) 来测试解析器的功能。


[def]: https://github.com/xiao-k233/omci-wireshark-dissector/raw/master/wirehark-about.png