# p4v的下载和使用


## ***下载P4V***

*URL: https://portal.perforce.com/s/downloads?product=Helix%20Visual%20Client%20%28P4V%29*

## ***安装后***

*打开P4V，输入服务器地址和用户名（这些信息在安装服务器或配置 P4 Cloud 时已创建）*

## ***创建 Depot（仓库）***

安装好 Unreal Engine 和 Perforce P4后，需要为项目创建一个 Depot——即你在 Perforce 服务器上用于存储项目的主目录。

操作步骤如下：

1. 打开 P4V，点击菜单栏 Tools > Administration ，进入 P4 Admin
2. 在 P4 Admin 中切换到 Depots 选项卡
3. 点击菜单 File > New > Depot…
4. 给 depot 命名（例如 “ProjectName”）注意：名称不能包含空格，且一旦设定无法更改
5. 将 Depot 类型设置为默认值 “stream”，然后点击 OK 创建 depot

## **设置 typemap（类型映射）**

在向服务器添加文件之前，你需要设置 typemap，这样 P4 服务器才能正确处理不同类型的文件。例如，当你编辑 .uasset 或 .umap 这类二进制文件时，typemap 可以指示服务器对这些文件进行独占锁定，从而避免多人同时编辑造成的冲突。

此步骤只需在首次设置服务器时执行一次：

1. 在 P4V 的左侧 Depot 面板中右键任意 depot，选择 Open Command Window Here
2. 这将打开一个终端窗口，在终端中输入命令：p4 typemap
3. 此操作将在你的默认文本编辑器（通常是记事本）中打开 typemap 文件
4. 大多数用户可以直接复制粘贴 Game Engine Universal Typemap
5. 保存并关闭文件。如果终端显示 “Typemap saved”，则表示操作成功

## **创建 Mainline Stream（主线流）**

Perforce Streams 可以简化分支和合并操作，自动化开发与发布流程。虽然 Streams 功能非常强大，但建议先从一个 mainline stream 开始，后续再随着项目的发展逐步扩展。

操作步骤如下：

1. 返回 P4V（不是 P4 Admin），点击 File > New > Stream…
2. 为 stream 命名（常见的命名方式是使用简短的项目代码 + “_main”，例如 “ABC_main”）
3. 在 Depot 字段中，选择你刚刚为该项目创建的 streams depot
4. 确保 Stream type（流类型）设置为 mainline
5. 取消勾选 Create a workspace to use with this stream （稍后再做这一步）
6. 取消勾选 Populate the mainline stream after it is created （稍后再做这一步）
7. 稍后会设置 p4ignore 文件，但为了更加安全，可以先确保一些文件夹始终被忽略。切换到“高级”选项卡，在 Ignored 字段中添加以下内容，然后点击“确定”：

```text
/Saved/...
/Intermediate/...
/DerivedDataCache/...
/FileOpenOrder/...
```

