# steam deck


## ***前言***

*最近在折腾steam deck 放一个博客存一些命令，以防格式化或者啥的，又得重弄*

*但是专门为deck弄一个分类不太好，所以放生活吧~*

*在前言放一个niu b的网站: https://ohmydeck.net/*

## ***注意事项***

1. *千万不能把桌面模式改成中文 会出不少问题 如果改了就恢复出厂!!!*

## ***前置***

1. *设置密码*

   ```cpp
   passwd
   ```

2. *解锁只读权限*

   ```bash
   sudo steamos-readonly disable
   ```

## ***加速桌面模式下的商店***

```bash
sudo flatpak remote-modify flathub --url=https://mirror.sjtu.edu.cn/flathub
```

## ***安装UU加速器插件***

```bash
curl -s uudeck.com|sudo sh
```


