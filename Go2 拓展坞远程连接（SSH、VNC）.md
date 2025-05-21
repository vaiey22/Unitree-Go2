# Go2 拓展坞远程连接说明文档（SSH & VNC 分开讲解）

## 📡 第一部分：通过 SSH 远程连接拓展坞

### 🔍 适用场景：

- 编译、运行 ROS 节点
- 编辑代码（命令行环境）
- 启动 SLAM、建图等服务
- 远程操作机器狗系统级程序

### ✅ Step 1：获取拓展坞 IP 地址

你需要知道拓展坞当前的局域网 IP，例如：`192.168.31.104`

在本地终端或设备上：

```
ping 192.168.31.104
```

确认网络联通即可。

### ✅ Step 2：用 SSH 登录（主机平台不限）

```
ssh ubuntu@192.168.31.104
```

首次登录会提示是否信任主机，输入 `yes` 并回车。 如果设置了密码，会要求输入 Ubuntu 用户密码。

### ✅ Step 3：登录成功后你可以做什么？

- 进入 ROS2 开发环境：

  ```
  source /opt/ros/foxy/setup.bash
  ```

- 启动 ROS 节点：

  ```
  ros2 run my_pkg my_node
  ```

- 查看系统资源：

  ```
  top
  htop
  ```

- 编写/调试 Python / C++ 代码

- 使用 colcon 编译你的工作区

## 🖥️ 第二部分：通过 VNC 可视化访问拓展坞桌面

### 🔍 适用场景：

- 使用图形界面工具（如 RViz、rqt、图形化终端）
- 拓展坞没有显示器，但你希望看到完整 Ubuntu 桌面
- 想通过鼠标点击操作而不是纯命令行

### ✅ Step 1：在拓展坞 Ubuntu 上安装 VNC 服务器

使用 `x11vnc`：

```
sudo apt update
sudo apt install x11vnc
```

### ✅ Step 2：设置 VNC 密码

```
x11vnc -storepasswd
```

系统会提示你输入访问密码，用于连接时验证身份。

### ✅ Step 3：启动 VNC 服务

```
x11vnc -usepw -forever -display :0
```

> 如果系统没有 GUI 桌面（比如只装了 server），你需要先安装：
>
> ```
> sudo apt install xfce4 xfce4-goodies
> ```

### ✅ Step 4：从本地设备连接 VNC

#### Windows 用户：

- 安装 RealVNC Viewer
- 打开并输入：`192.168.31.104:5900`
- 输入你在拓展坞设置的 VNC 密码

#### Mac / Ubuntu 用户：

```
vncviewer 192.168.31.104:5900
```

### ✅ Step 5（可选）：配置 VNC 自动启动

```
sudo nano /etc/systemd/system/x11vnc.service
```

写入以下内容：

```
[Unit]
Description=Start x11vnc at boot
After=graphical.target

[Service]
ExecStart=/usr/bin/x11vnc -forever -usepw -display :0
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

然后启用服务：

```
sudo systemctl daemon-reexec
sudo systemctl enable x11vnc.service
```

！！

| 问题                          | 答案                                              |
| ----------------------------- | ------------------------------------------------- |
| VNC 要装在哪？                | ✔ 在拓展坞的 Ubuntu 上安装服务端 `x11vnc`         |
| 我的电脑需要安装什么？        | ✔ 只要装个 VNC Viewer 工具即可                    |
| 可以在主机上看到 ROS 界面吗？ | ✔ 是的！你就像坐在拓展坞面前一样操作 RViz、rqt 等 |



## ✅ 总结对比

| 项目         | SSH                              | VNC                                        |
| ------------ | -------------------------------- | ------------------------------------------ |
| 是否图形界面 | ❌ 纯命令行                       | ✅ 远程桌面                                 |
| 适合操作     | 启动 ROS 节点、写代码、调服务    | RViz、rqt、SLAM 可视化                     |
| 安装是否需要 | 主机不需要任何工具，直接终端即可 | 拓展坞需安装 VNC 服务端，主机装 VNC Viewer |

如需同时使用 RViz 和终端开发，建议“两者都装”。