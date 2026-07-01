# Ubuntu搭建P4服务器


## ***下载并安装 P4D 核心程序***
*（两个实例共用一个执行文件）*

```bash
cd /tmp
wget https://www.perforce.com/downloads/perforce/r24.1/bin.linux26x86_64/p4d
chmod +x p4d
sudo mv p4d /usr/local/bin/
```

## ***创建服务用户与独立数据目录***
```bash
sudo useradd -m perforce
# 创建 Simple 实例目录
sudo mkdir -p /opt/perforce/Simple
# 创建 Sprout 实例目录
sudo mkdir -p /opt/perforce/Sprout
# 统一授权
sudo chown -R perforce:perforce /opt/perforce
```

## ***初始化两个 Perforce 实例 (Unicode 模式)***
```bash
# 初始化 Simple (1666)
sudo -u perforce p4d -r /opt/perforce/Simple -p 1666 -xi

# 初始化 Sprout (1667)
sudo -u perforce p4d -r /opt/perforce/Sprout -p 1667 -xi
```

## ***配置 Systemd 服务文件***
*我们需要创建两个独立的服务文件来管理这两个实例。*

### ***配置 Simple 服务：***
```bash
sudo vi /etc/systemd/system/p4d-simple.service
```
*写入以下内容：*

```ini
[Unit]
Description=Perforce Server - Simple (Port 1666)
After=network.target

[Service]
Type=simple
User=perforce
ExecStart=/usr/local/bin/p4d -r /opt/perforce/Simple -p 1666
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

### ***配置 Sprout 服务：***
```bash
sudo vi /etc/systemd/system/p4d-sprout.service
```
*写入以下内容：*

```ini
[Unit]
Description=Perforce Server - Sprout (Port 1667)
After=network.target

[Service]
Type=simple
User=perforce
ExecStart=/usr/local/bin/p4d -r /opt/perforce/Sprout -p 1667
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

## ***启用并启动服务***
```bash
sudo systemctl daemon-reload

# 启动 Simple
sudo systemctl enable p4d-simple
sudo systemctl start p4d-simple

# 启动 Sprout
sudo systemctl enable p4d-sprout
sudo systemctl start p4d-sprout
```

### ***检查状态与防火墙设置***
```bash
# 检查状态
sudo systemctl status p4d-simple
sudo systemctl status p4d-sprout

# 开放防火墙端口
sudo ufw allow 1666
sudo ufw allow 1667
```

