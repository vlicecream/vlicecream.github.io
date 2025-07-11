# Rider & Unreal 配置P4V


## ***Rider***

### ***配置P4V信息***

1. *打开 Rider 设置: File -> Settings (或者按 Ctrl+Alt+S)。*
2. *导航到 Version Control -> Perforce。*
3. *配置连接参数:*
   - ***Port**: 输入你的服务器地址和端口，格式为 host:port (例如 p4.mycompany.com:1666)*
   - ***User**: 输入你的 Perforce 用户名。*
   - ***Workspace**: 输入你为这个项目创建的 **工作区名称**。这是 Rider 用来确定哪些文件在你的控制之下的关键。*
   - ***Path to P4 executable**: 通常你可以留空，Rider 会自动从系统 PATH 中找到 p4.exe。如果找不到，你可以点击 ... 按钮手动指定 p4.exe 文件的完整路径。*
   - ***Path to P4VC executable**: 通常你可以留空，Rider 会自动从系统 PATH 中找到 p4v.exe。如果找不到，你可以点击 ... 按钮手动指定 p4v.exe 文件的完整路径。*
4. *测试连接:*
   - *点击 Test Connection 按钮。*
   - *如果一切配置正确，你会看到一个 "Connection successful" 的提示。*
   - *如果提示需要输入密码，它会弹出一个对话框让你输入。输入后可以选择“记住密码”。*
   - *如果连接失败，请仔细检查你的服务器地址、用户名和工作区名称是否拼写正确。*

### ***将项目与 Perforce 关联***

*即使配置好了连接，你还需要告诉 Rider 这个**特定的项目**要使用 Perforce 进行版本控制。*

1. *打开 Rider 设置: File -> Settings (或者按 Ctrl+Alt+S)。*
2. *导航到 Version Control。*
3. *在右侧的 Directory Mappings 区域，你会看到你的项目根目录。（没有就新加一个）*
4. *在 VCS 这一列，点击下拉菜单，选择 **Perforce**。*
5. *点击 Apply 和 OK。*

## ***Unreal***

***按照红框点击，并输入其配置即可***

![image-20250711234626704](https://raw.githubusercontent.com/vlicecream/cloudImage/main/image-20250711234626704.png)

