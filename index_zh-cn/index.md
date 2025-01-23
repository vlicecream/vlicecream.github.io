# Steam Deck


# SteamDeck

*最近在折腾steam deck 放一个博客存一些命令，以防格式化或者啥的，又得重弄*

*但是专门为deck弄一个分类不太好，所以放生活吧~*

*在前言放一个niu b的网站: https://ohmydeck.net/*

*还有放一些游戏的攻略及一些相关东西*

## ***刚拿到机子必须要做的事***

### ***注意事项***

1. *千万不能把桌面模式改成中文 会出不少问题 如果改了就恢复出厂!!!*

### ***前置***

1. *进入桌面模式*

2. *设置密码*

   ```
   passwd
   ```

3. *解锁只读权限*

   ```
   sudo steamos-readonly disable
   ```

### ***加速桌面模式下的商店***

```
sudo flatpak remote-modify flathub --url=https://mirror.sjtu.edu.cn/flathub
```

## ***桌面模式下 安装中文输入法***

1. *首先，打开Discover应用商店，搜索“input”并找到Fcitx 5应用。点击安装按钮，开始下载并安装这款输入法应用*
2. *安装完成后，进入Fcitx 5的详情页面，找到Addons选项。在这里，你可以找到并安装Chinese Addons，为你的输入法增添中文支持。*
3. *安装完毕后，记得先启动Fcitx 5哦！然后，你可以通过右键点击状态栏中的输入法图标，选择Configure来配置你的输入法。在配置窗口中，输入“pinyin”并添加到左侧，即可开启拼音输入法功能。*
4. *最后，为了确保每次开机都能自动启动Fcitx 5和中文输入法，你需要进入系统设置，选择Startup and shutdown选项，然后添加Fcitx 5到自启动列表中*

## ***安装UU加速器插件***

```
curl -s uudeck.com|sudo sh
```

## ***Clash - ToMoon***

### ***安装***

*如果已经安装过 Plugin Loader 2.0 以上版本，直接从第 8 点开始即可。*

1. *打开到 Steam Deck 设置界面*
2. *系统 -> 系统设置 -> 打开开发者模式*
3. *回到设置向下翻，找到开发者 -> 打开 CEF 远程调试*
4. *等待 Steam Deck 重启*
5. *按电源键切换到 Desktop 桌面模式*
6. *打开 Konsole，输入 `curl -L http://dl.ohmydeck.net | sh` 安装 Plugin Loader*
7. *输入 `curl -L http://i.ohmydeck.net | sh` 安装 Tomoon*
8. *切换回到 Gamming 游戏模式，按下右侧摇杆下的快捷按钮（三个点的按钮），可以看到多了一个 Decky 插件面板*

### ***使用***

1. *打开 Manage Subscriptions，添加你服务商提供的 Clash 订阅链接并下载*
2. *下载完成后，切换回主界面选择订阅并点击启动*
3. *在桌面模式可通过浏览器 http://127.0.0.1:9090/ui 打开仪表盘*

### ***复原 DNS，解决插件消失而导致无法联网***

*在桌面模式打开 Konsole，输入以下命令。
**注意：不熟悉 Linux 的朋友请务必输入正确，看到命令执行完后有错误出现就是输入错了。***

```shell
sudo chattr -i /etc/resolv.conf
sudo systemctl stop systemd-resolved
sudo chmod a+w /etc/NetworkManager/conf.d/dns.conf
sudo echo -e "[main]\ndns=auto"  > /etc/NetworkManager/conf.d/dns.conf
sudo nmcli general reload
```

*如果安装的是 `v0.0.5` *(2022/11/18)* 以上版本，可以使用脚本直接恢复。*

```shell
bash ~/tomoon_recover.sh
```
