# Ubuntu搭建P4服务器


## ***下载并安装 p4d***

```bash
cd /tmp
wget https://www.perforce.com/downloads/perforce/r24.1/bin.linux26x86_64/p4d
chmod +x p4d
sudo mv p4d /usr/local/bin/
```

## ***创建服务用户和数据目录***

```bash
sudo useradd -m perforce
sudo mkdir -p /opt/perforce/depots
sudo chown -R perforce:perforce /opt/perforce
```

## ***设置环境变量（为 `perforce` 用户）***

*`sudo -i -u perforce bash` 会进入到 perforce 命令行*

```bash
sudo -i -u perforce bash
vi ~/.bashrc
```

### ***添加以下内容***

```bash
export P4PORT=1666
export P4ROOT=/opt/perforce/depots
```

### ***生效***

```bash
source ~/.bashrc
```

### ***初始化 Perforce 服务端***

```bash
p4d -r $P4ROOT -p $P4PORT -xi
```

### ***启动服务***

```bash
p4d -r $P4ROOT -p $P4PORT
```

## ***配置 systemd 服务***

*创建服务文件*

```bash
sudo vi /etc/systemd/system/p4d.service
```

*写入以下内容：*

```ini
[Unit]
Description=Perforce Helix Core Server
After=network.target

[Service]
Type=simple
User=perforce
ExecStart=/usr/local/bin/p4d -r /opt/perforce/depots -p 1666
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

*启用并启动服务：*

```bash
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable p4d
sudo systemctl start p4d
```

*检查状态*

```bash
sudo systemctl status p4d
```

*开放防火墙端口（如有需要）*

```bash
sudo ufw allow 1666
```


