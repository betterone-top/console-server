[English](README.md) | [Tiếng Việt](README.vi.md) | [简体中文](README.zh-CN.md)

# Betterone Console Server

纯软件部署，无需专用硬件。可运行在普通电脑或树莓派上。

通过 SSH、Web 终端、Telnet 和 Raw TCP 访问普通电脑或树莓派上的物理串口，并提供 REST API 管理配置与会话。最多 48 路控制台，免费版可使用编号 1–2；商业授权容量和维护覆盖以许可证为准。

## 快速上手

Windows 服务版运行 `betterone-cs_<版本>_Windows_x86_64_setup.exe`；Linux 安装对应架构 deb/rpm，安装后服务自动启动。首次打开网页，立即设置管理员密码；没有默认密码。安装与升级细节见[客户操作手册](docs/customer/cn/USER_MANUAL.md)。

绿色版解压后：

```sh
./betterone-cs --init-config
# Linux 普通用户无法绑定 80 时，在 config/settings.toml 中取消注释 http_port 并改为 8080。
./betterone-cs
```

Windows 使用 `betterone-cs.exe`。默认 portable 模式，配置与数据在程序旁的 `config/`、`data/`。生成的 `settings.toml` 所有项都是注释掉的默认值，只有取消注释的项才生效；网页上修改的 Web 地址、HTTPS 和审计设置保存在数据库中，不写回该文件。访问 `http://<服务器IP>:<端口>/`，设置至少 8 个字符、不能仅含字母或仅含数字的管理员密码，然后在网页绑定串口。

## 接入与安全

| 接入方式 | 默认端口 | 默认状态 |
| --- | --- | --- |
| Web 管理 / API / Web 终端 | 80 | 开启；控制台 WebSocket 路径 `/console/N`，默认需要登录 |
| SSH | 6000 + N | 开启，默认需要密码或公钥认证 |
| Telnet | 7000 + N | 关闭，开启后默认需要登录 |
| Raw TCP | 5000 + N | 关闭，开启后默认需要登录 |

`N` 为控制台编号 1–48。新建配置的监听地址默认是 `0.0.0.0`；Telnet 和 Raw TCP 默认不启动监听，开启前请核对监听地址。

管理员可以分别设置每个控制台四种协议是否需要登录；关闭后，能访问该协议监听地址的人可直接使用该控制台。Telnet 和 Raw TCP 不加密，即使开启登录，账号密码仍以明文传输；需要加密时使用 SSH、HTTPS Web 终端或受保护的隧道。不要把尚未设置管理员密码的设备暴露到不可信网络。

各协议还可分别控制辅助提示，SSH 和 WebSocket 默认开启，Telnet 和 Raw TCP 默认关闭。关闭后不输出串口状态、写入权限等服务端说明，串口数据及登录必需的账号、密码提示仍保留。

## 串口操作与批量配置

物理串口页面中，已绑定串口可打开对应控制台；未绑定串口可由管理员或工程师直接「打开串口」。直接打开时不保存设置，同一串口同时只允许一个直连会话；绑定到控制台后，当前串口参数一并保存。控制台支持批量修改串口参数、协议开关、登录要求、辅助提示、连接上限及写入模式。

系统页可导出全部 48 个控制台的备注、硬件绑定、串口参数、协议开关、登录要求、辅助提示、连接上限、写入模式和监听地址；不包含用户、API Key、许可证或系统设置。导入提供两种方式：

- **批量配置**（默认）：按控制台编号应用通用设置，保留目标机器的 COM/TTY、USB 位置和身份绑定、监听 IP 及端口。
- **完整恢复**：覆盖全部端口配置，适合同一台机器恢复。

同品牌同型号也可能有不同 COM 名称、USB 序列号、物理位置和本机 IP，不能据此直接照搬完整备份；跨机器应用前需确认目标串口支持所选参数。导入可能中断现有连接。

发布目标为 Windows amd64，以及 Linux amd64、arm64、armv7（GOARM=7）。按目标操作系统架构选择，物理 USB 串口、驱动和目标系统安装效果仍需实机验收。

## 授权与配置

当前直接售卖的商业授权为 **8P、16P、48P** 三档，其他档位不直接售卖。免费版可使用控制台 1–2；实际商业容量以签发的许可证为准。

授权永久有效，维护期只限制可使用的软件发布日期，不是当前时间上的运行到期日。维护期按设备计算；在线 ACT 激活、RNW 续期、离线请求和文件导入均在授权页操作。设备身份使用 hardware/install/generated，不使用网卡 MAC。

TOML 只需写要覆盖的字段；未写字段使用程序默认值。正式构建的授权服务器和官网办理入口固定使用内置地址（授权服务器为 `https://lic.cs.betterone.top`），忽略配置中的覆盖值及空值。只有 `-tags tst` 测试构建允许覆盖 `license_server`、`license_portal_url`、`license_offline_url`，空值可关闭对应功能或入口。项目官网固定为 `https://cs.betterone.top`。安装版升级会重启并断开会话；卸载保留配置与数据，恢复出厂另需本机确认。

## 项目与联系

官网：[cs.betterone.top](https://cs.betterone.top)

GitHub：[betterone-top/console-server](https://github.com/betterone-top/console-server)

邮箱：[betterone-top@hotmail.com](mailto:betterone-top@hotmail.com)
