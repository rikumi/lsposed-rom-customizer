# 构建、交付与发布

## 增量构建

使用项目Gradle wrapper和已配置任务，核对JDK toolchain、Android SDK和实际variant。来源工程使用过JDK21，但不是所有模块的固定要求；按目标项目配置选版本。复制工程缺`local.properties`时使用正确`sdk.dir`或`ANDROID_HOME`，不要复制另一台机器绝对路径。

示例需要替换为本机真实值：

```sh
JAVA_HOME=/path/to/required-jdk ANDROID_HOME=/path/to/android-sdk ./gradlew :app:assembleDebug
```

先检查修改文件与`git diff --check`，必要时诊断，再运行增量任务。不默认`clean`。构建显示UP-TO-DATE可接受；以任务结果和产物variant为依据，不因别人刚构建过而怀疑产物。

macOS临时目录限制可能影响JADX、Gradle或git/xcrun；确认可写临时目录后可设置`JAVA_TOOL_OPTIONS=-Djava.io.tmpdir=<目录>`等适当变量。Gradle cache锁、受限socket或SDK缺失是环境问题，区分于代码编译失败，不能因见到警告就称失败或成功。

运行关键逻辑测试和构建任务时检查退出码及必要产物；不虚报未实际执行的验证。构建被无关代理改动阻止时指出具体影响，不越权修复。

## APK核对与发送

从构建输出定位真实APK文件名，不固定为`app-debug.apk`。核对APK包名、版本、签名和模块入口/作用域配置确实打包；适当使用`apksigner verify`、`aapt/aapt2 dump badging`和`unzip -l`。

每次Android代码修改后按当前工作约定增量构建并交付产物。用户要求发送文件时使用可用文件上传工具；仅给路径不算发送。失败重试依据工具能力，路径错误可在支持时换为绝对路径一次；不无限重试，也不谎称附件已送达。

文件工具限制工作区时遵守实际能力；不把用户要求放在项目外的Skill/资料偷偷复制入项目。工具确实无法发送则说明限制，使用已获授权的其他交付方式。

APK交付不授权设备安装。安装、数据修改、进程重载与重启分别遵守会话/项目授权。未经实机验证，交付说明明确列出待验证状态。

## 包名迁移

同时检查：Gradle applicationId/namespace、Java/Kotlin包声明和目录、Manifest组件/Application、Xposed入口、Provider authority/URI、资源路径、远程设置标识、打包配置、README、构建输出、模块仓库身份与发布目标。

迁移后直接核对APK，不只搜索源码。原数据迁移和旧包共存行为按需求设计；不要为改名清用户数据。

## Git和PR

只有用户要求时提交/push/创建PR；既有授权按会话继续生效。先读当前branch、remote、工作树与diff，避免把他人的未完成改动混入未经要求的提交。

GitHub提交采用项目约定的英文conventional信息。用户要求提交整个项目时按其范围处理所有改动，可分主题；不把新文件自动排除在外。

远程前进时fetch、分析分歧、rebase并保留双方功能；冲突修复后对最终树验证，再正常push，避免为省事force push。保持原文件换行，历史CRLF转LF曾产生大面积无关diff和尾空格错误。

push后以`git ls-remote <remote> refs/heads/<branch>`与本地HEAD比较，不只依赖本地tracking ref。正式PR必须包含已完成并验证的最终实现；标题/正文面向最终行为，PR head等于最新已push提交。若用户要求持续同步PR，后续授权范围内保持更新并确认。

## 发布和独立凭据

复用另一模块的README/workflow结构时，替换仓库、包名、版本、分支、发布目标和secret名称；不同工程凭据保持独立，缺secret时不借用另一个工程token。

构建成功、模块仓库申请批准、包名迁移push都不能证明release成功。发布授权后核对CI日志、目标tag/release、APK附件和必要镜像内容；跨仓库权限错误根据日志解决，不扩大凭据用途。

签名密钥、token、机器路径和设备隐私数据不进入Skill、issue或Gist。独立发布任务使用该工程授权凭据，不从参考APK目录意外打包keystore。



