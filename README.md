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
   - 对于 Windows，插件目录通常为`%APPDATA%\Wireshark\plugins`(直接输入到文件资源管理器的地址栏中，如没有，可以新建)
   - 对于 Ubuntu、Debian、Fedora 和 Redhat 系统，插件目录通常为：`$HOME/.config/wireshark/plugins`

完成以上步骤后，插件即可正常使用。

## Lantiq/MaxLinear 芯片组教程

如果您使用的是 WAS-110 设备（或其他基于 prx126 方案的 PON Stick），并且安装了最新版本的 8311 固件分支，可以按照以下步骤抓取 OMCI 数据包：

1. 通过 SSH 连接到设备
2. 执行命令 `8311-capture-omci-msg.sh` 开始抓包
3. 可以通过断开并重新连接光纤来触发 PON 认证过程，这将生成完整的 OMCI 消息记录
4. 抓包完成后，系统会生成一个 hex 格式的文件

> [!CAUTION]
> 生成的 hex 文件中**包含 LOID 等设备敏感信息**，请勿公开分享此文件。

假如您采用其他固件，或者 MA5671A 等老芯片，可以采用如下方式抓包：

1. `omci_pipe.sh mdfe /tmp/omci.log`开始抓包
2. 等待一会，期间可以`cat`一下`/tmp/omci.log`来看抓包多少了
3. `omci_pipe.sh mdd`停止抓包
4. `awk '/^rx\|/ || /^tx\|/ {gsub(/.*\|/, ""); print "000000 " $0}' "/tmp/omci.log" > "/tmp/omci.hex"`使其转换为 hex 文件
5. 使用`scp`回传
6. 在 Wireshark 中，`文件-从Hex转储导入`
7. 上面选择回传的 hex 文件，下方封装类型选择 Ethernet,更下方点击以太网，填入以太类型（十六进制）`88b5`
8. 不会看图
   ![导入Hex文件步骤1](https://github.com/xiao-k233/omci-wireshark-dissector/raw/master/guide-one.png)
   ![导入Hex文件步骤2](https://github.com/xiao-k233/omci-wireshark-dissector/raw/master/guide-two.png)

## 使用示例

您可以使用我们提供的 [示例数据包](https://github.com/xiao-k233/omci-wireshark-dissector/blob/master/omci-example.pcap?raw=true) 来测试解析器的功能。

[def]: https://github.com/xiao-k233/omci-wireshark-dissector/raw/master/wirehark-about.png
