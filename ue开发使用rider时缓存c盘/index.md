# UE开发使用Rider时缓存干爆C盘的解决方案


*我们在使用Rider开发UE时，Ride会为每一个项目创建一个解决方案缓存，如果开几个新项目写测试demo，我们的C盘会逐渐捉急*

*不知道官方怎么想的，默认在C盘就算了，还没法改*

*目前没有找到这个路径相关的设置，只能另辟蹊径*

*我们既然改不掉这个路径，那就将这个路径映射到其他路径上*

*windows下，可以使用mklink指令，创建类似linux的软连接*

*首先把C:\Users"用户名"\AppData\Local\JetBrains这个文件夹移动到你希望他出现的位置*

*然后用管理员身份打开CMD*

*使用下面的指令，把里面%引起来的内容替换成你的东西*

```cpp
mklink /j "C:\Users\%你的用户名%\AppData\Local\JetBrains" "%你希望这个缓存所在的路径%"
 
我的就是
mklink /j C:\Users\User\AppData\Local\JetBrains G:\JetCache\JetBrains
```

*他已经不占用C盘空间了，也不影响Rider解析，大功告成，告别C盘焦虑*

