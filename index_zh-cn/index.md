# 虚拟机安装Win11 TPM报错


1. *按下 `shift + F10`，输入 `regedit`*

2. *在注册表编辑器中，导航到以下路径：*

   `HKEY_LOCAL_MACHINE\SYSTEM\Setup`

3. *在`Setup`上右键，选择 新建 -> 项，并将新项命名为 `LabConfig`*

4. *选中新建的 `LabConfig`，然后再右侧空白处右键*

   * *新建 -> DWORD（32位），命名为 `BypassTPMCheck`，并将其值设置为 1*
   * *新建 -> DWORD（32位），命名为 `BypassSecureBootCheck`，并将其值设置为 1*
   * *如果还遇到 CPU 报错，可以在创建一个 `BypassCPUCheck`，并将其值设置为 1*

5. *关闭，继续安装*
