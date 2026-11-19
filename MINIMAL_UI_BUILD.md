# ZeroTermux 简约原生 UI

## 当前状态

基于 ZeroTermux 原生项目提交 `83426f4` 修改，不是网页终端或聊天客户端。

已修改原生主题、侧栏背景、菜单卡片、会话条目和按钮样式。原有控件 ID、终端引擎、JNI、包名、数据路径、备份恢复、容器、换源、Linux、X11、ADB、文件管理和 AI 功能实现未删除。

静态检查已确认：受影响布局的原控件 ID 全部保留；终端核心模块和构建配置未修改；Android XML 可解析。

尚未生成 APK，也没有完成真机验证。当前实际构建在缺少 `JAVA_HOME` / `java` 时停止，不把源码修改当成已正常运行的安装包。

## 安装兼容性

保留 `com.termux` 包名和原来的 target SDK 28，避免破坏 Termux 的路径、执行环境和插件兼容性。

源码中的 `phone.jks` 证书与当前设备安装包证书一致，可以采用同签名升级方案。但仍须验证最终 APK 的证书与版本，再由用户确认安装；不卸载原应用、不清空数据，不自动执行安装。

## 构建要求

- JDK 17 或兼容的更新版本。
- Android SDK Platform 36。
- Android NDK `29.0.14206865`。
- 原项目 Gradle Wrapper 与完整的原生资源、AAR 和插件附件。

本工作区为了先修改界面采用稀疏检出。正式构建前要获取完整 app 内容，不能通过删除二进制资源或功能依赖来凑出安装包：

```bash
git sparse-checkout set --cone app terminal-emulator terminal-view termux-shared gradle config scripts .github
./gradlew :app:assembleDebug -Darch=arm64 --no-daemon
```

需要通用安装包时，用 `-Darch=universal`。不要改包名、目标 SDK、签名配置或终端核心来绕过构建问题。

## 云端构建

原仓库已经提供 `.github/workflows/build.yml`。可以在用户授权的 GitHub 仓库中使用 GitHub Actions：选择 `arm64` 或 `universal`，构建 Debug APK，下载产物后检查证书并安装测试。

本次没有在用户账号中创建远端仓库、上传代码或触发云构建。需要用户提供可授权的 GitHub 仓库或 Android 构建机，不要求把密码或访问令牌贴进聊天。

## 真机验收

最终 APK 至少应检查：终端输入与命令、Ctrl/Alt/ESC 和软键盘、会话切换/新建/退出、复制粘贴、包管理、备份恢复、容器与换源、Linux 入口、X11/VNC/ADB、文件管理、AI 与扩展菜单、原数据保留。

这些入口已保留在源代码中，但只有编译、签名校验和实际使用测试通过后，才可以声称 APK 正常可用。
