---
title: 华为云服务器debain11.1 安装zerotier,并改成moon节点
date: 2024-04-12 16:01:23
---

看到华为云，46元一年2c2g的云服务器，果断入手。首先用它“安装zerotier,并改成moon节点”，据说可以提升两台机器之间的网速

## Debian 11.1系统中安装ZeroTier的步骤

1. 首先，您需要更新您的系统以确保所有的包都是最新的。这可以通过运行以下命令来完成：

```
apt update && apt upgrade
```

2. 接下来，您需要下载ZeroTier的安装脚本。可以通过运行以下命令来实现：

```
curl -s https://install.zerotier.com/ | bash
这个命令会从ZeroTier的官方网站下载安装脚本并执行它。
```

3. 安装完成后，您需要启动ZeroTier服务。这可以通过执行以下命令来完成：

```
systemctl start zerotier-one
```
并且，为了确保ZeroTier服务在系统启动时自动运行，您可以启用它：
```
systemctl enable zerotier-one
```


4. 现在，您可以使用zerotier-cli命令来管理ZeroTier网络。要查看当前ZeroTier的状态，可以使用：

```
zerotier-cli status
```
要加入一个已有的ZeroTier网络，您需要使用以下命令，其中<Network ID>是您要加入的网络的ID：
```
zerotier-cli join <Network ID>
```

## 改成moon节点

1. 生成Moon配置文件：
进入ZeroTier的配置目录并生成Moon配置文件：
```
cd /var/lib/zerotier-one/
zerotier-idtool initmoon identity.public > moon.json
```
2. 编辑Moon配置文件：
使用文本编辑器修改moon.json文件，添加您的华为云服务器的公网IP地址和端口（默认为9993）到stableEndpoints数组中：
```
# 使用vim或其他文本编辑器打开文件
vim moon.json
# 修改stableEndpoints项，例如：
"stableEndpoints": ["<your_public_ip>/9993"]
```
3. 生成并应用Moon文件：
使用zerotier-idtool生成Moon文件，并将其移动到moons.d目录下：
```
zerotier-idtool genmoon moon.json
mkdir -p moons.d
mv 000000xxxxxx.moon moons.d/
```
4. 重启ZeroTier服务：
重启ZeroTier服务以应用新的Moon配置：

```
systemctl restart zerotier-one.service
```
5. 验证Moon节点配置：
使用zerotier-cli命令查看Moon节点是否已成功加入网络：
```
zerotier-cli listmoons
```
6. 配置防火墙（如果需要）：
确保您的华为云服务器防火墙允许UDP协议通过端口9993，以便Moon节点可以正常工作。

## 如何验证Moon节点配置成功

1. 查看Moon节点列表：通过执行以下命令来查看当前加入的Moon节点列表：
```
zerotier-cli listmoons
```
如果配置成功，您应该能够看到一个或多个Moon节点的列表，其中应包括您刚刚配置的Moon节点。

2. 查看Peer列表：此外，您还可以查看所有ZeroTier网络中的Peer（节点），包括Moon节点。使用以下命令：
```
zerotier-cli listpeers
```
在输出中，您应该能够看到包含"MOON"标记的行，这表明您的Moon节点已经成功加入网络并被识别。

3. 测试网络连接：从网络中的其他节点（Leaf节点）测试连接到Moon节点。您可以通过在Leaf节点上运行zerotier-cli listpeers命令来查看是否能够看到Moon节点的IP地址和状态。

检查防火墙和端口：确认服务器的防火墙设置允许UDP流量通过端口9993，这是ZeroTier默认使用的端口。

## 遇到的问题

1. 服务器上使用 zerotier-cli listpeers和现有客户端使用zerotier-cli listpeers，都没有展示moon节点，只有LEAF和PLANET节点

解决方案：需要重新添加客户端，通过Moon节点连接到ZeroTier网络

- Windows系统：在ZeroTier的安装目录（默认为C:\ProgramData\ZeroTier\One）下创建一个名为moons.d的文件夹。然后将您的Moon节点的.moon文件复制到这个文件夹中。接下来，您可以通过重启ZeroTier服务或使用命令行工具来使配置生效：
```
zerotier-cli restart
```
或者在服务管理器中重启ZeroTier服务


- Linux系统：在/etc/zerotier/one/目录下创建一个名为moons.d的文件夹（如果尚未存在）。将Moon节点的.moon文件复制到这个文件夹中。然后重启ZeroTier服务：
```
zerotier-cli restart
```
或者使用其他适合您系统的重启命令

- macOS系统：在/Library/Application Support/ZeroTier/One/目录下创建一个名为moons.d的文件夹。将Moon节点的.moon文件复制到这个文件夹中。然后重启ZeroTier服务，可以通过系统偏好设置中的ZeroTier面板或使用命令行：
```
zerotier-cli restart
```
来完成重启


- 简单方法：zerotier-cli orbit <Moon节点ID>