# 进度报告一

## 一、任务理解

这节课要做的事：用 Flutter 把 hello\_world 跑起来，能同时在 Web 和 Android 模拟器上看到那个计数器界面，代码用 Git 管理并推到远程仓库。

## 二、环境与工具

- 操作系统：Windows 11
- Flutter 版本：3.47.4 stable / Dart 3.13.3
- 运行目标：Chrome（Web 端）、Android 14 API34 模拟器（Medium\_Phone）
- TraeWork：用于辅助查 Gradle 镜像、解释报错、审查 Git 状态
- IDE：Trae + Android Studio

## 三、过程记录

| 时间   | 事件                                        | 关键输出/文件                                                 |
| :--- | :---------------------------------------- | :------------------------------------------------------ |
| 9.16 | 安装 Flutter SDK 到 F 盘、配 PATH、装 Android SDK | `F:\flutter\flutter`、`F:\AndroidSDK`                    |
| 9.17 | 创建 hello\_world 项目，git init + 首次提交        | commit `513ae48`                                        |
| 9.20 | Web 和 Android 模拟器各跑通一次，截图归档；Gradle 网络问题修复 | commit `37bc196`、`docs/web_counter.png` 等               |
| 9.22 | 关联 GitHub 远程仓库、写 README、修 README 重复块      | commit `9eac800`、`8c74b66`；仓库 penumbrawing/hello\_world |

## 四、关键代码（runApp 相关）

main.dart 第 3–5 行：

```dart
```

<br />

## 五、检查点结果

1. **flutter doctor 全绿**：✅ 2026.9.22 截图见 `docs/flutter_doctor.png`，7 项全勾，末尾 `No issues found!`
2. **HelloWorld 在 Web 运行**：✅ `flutter run -d chrome` 跑通，Chrome 自动打开计数器页面，截图 `docs/web_counter.png`
3. **HelloWorld 在模拟器运行**：✅ 在 Android Studio Device Manager 建了 Medium\_Phone API34，`flutter run -d emulator-5554` 跑通，截图 `docs/android_counter.png`
4. **仓库首次提交**：✅ `git log` 显示 513ae48 `feat: flutter create hello_world`
5. **Git 规范**：✅ 6 次提交全部使用 Conventional Commits 前缀（feat/docs/fix），`build/`、`.dart_tool/` 均未被跟踪（`git ls-files` 验证）

## 六、问题与调试

**问题：Gradle 下载超时，Android 构建失败**

- **现象**：执行 `flutter run -d emulator-5554` 后，到 `Running Gradle task 'assembleDebug'` 这一步卡住几十秒，最后报错 `java.net.SocketException: 远程主机强迫关闭了一个现有连接`，exit code 1。
- **定位**：看错误堆栈里有 `org.gradle.wrapper.Download.downloadInternal` 这一行，说明是 Gradle wrapper 在下载 Gradle 本体时失败。打开 `android/gradle/wrapper/gradle-wrapper.properties` 看，`distributionUrl` 指向 `https://services.gradle.org/distributions/gradle-9.3.1-all.zip`—— 境外服务器。浏览器试这个 URL 同样打不开，确认是网络问题。
- **解决**：把 `distributionUrl` 改成腾讯云镜像 `https://mirrors.cloud.tencent.com/gradle/gradle-9.3.1-all.zip` ；再把 `android/settings.gradle.kts` 的 `pluginManagement.repositories` 里在 `google()`、`mavenCentral()` 之前加上阿里云 Maven 镜像（gradle-plugin / google / public 三个）。
- **验证**：重新跑 `flutter run -d emulator-5554`，终端输出 `Download https://mirrors.cloud.tencent.com/gradle/...` ，几十秒下完，最终模拟器上出现计数器界面。

## 七、AI 使用记录

| 用途                | 指令摘要                                   | AI 输出（核心）                                           | 验证方式                        |
| :---------------- | :------------------------------------- | :-------------------------------------------------- | :-------------------------- |
| Gradle 镜像修复       | 贴报错堆栈，问什么原因                            | 指出是 wrapper 下载超时，给出腾讯云镜像 URL 和 settings 改法          | 改完文件重跑 `flutter run`，确认构建通过 |
| 解释 Dart 3 上下文推断简写 | 问 main.dart 第 31 行 `.fromSeed` 前为什么没类名 | 解释 Dart 3.0 引入的 contextual static access，编译器根据上下文推断 | 自己看 Dart 官方文档对照确认           |
| Git 工作流审查         | 让 AI 跑 `git log`、`git status`          | 指出提交信息缺空格、README 代码块重复等问题                           | 自己跑同样命令核对输出                 |
| runApp/Widget 概念  | 脱稿讲了一遍让 AI 挑逻辑漏洞                       | 指出"Widget 变成渲染树"是错的，缺 Element 层；缺 State 的解释         | 自己重讲一遍，逻辑能自洽                |

## 八、证据截图

- `docs/flutter_doctor.png`：7 项检查全绿，末尾 No issues found
- `docs/web_counter.png`：Chrome 中运行计数器，数字为 1（说明按钮点过）
- `docs/android_counter.png`：Medium\_Phone 模拟器运行计数器界面

## 九、自评

| 要求                 | 完成情况 | 备注                                           |
| :----------------- | :--- | :------------------------------------------- |
| 6.1 案例复现           | 完成   | hello\_world 多端运行通过，提交规范，截图齐                 |
| 6.2-1 安装记录         | 完成   | 装在 F 盘，过程有命令+结果+验证                           |
| 6.2-2 问题修复         | 完成   | Gradle 网络问题真实记录，定位到 wrapper 下载源              |
| 6.2-3 SDK/PATH 原理  | 完成   | 能说清 SDK 是写代码+运行时；平台工具链是各平台编译脚手架；PATH 是命令查找清单 |
| 6.2-4 main.dart 注释 | 完成   | 已逐行注释，包括 Dart 3 简写语法                         |
| 6.2-5 AI 边界        | 完成   | AI 只做信息提供和审查，命令执行、内容撰写均自己来                   |

## 十、下一步计划

通过main.dart实现自己的想法改 UI.
