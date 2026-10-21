# 轻语 0.5.1 对应源码与构建

本源码对应 `qingyu-0.5.1-beta.apk`，应用代码按 GNU GPL v3 或更高版本提供。第三方源码、词库和模型保留各自许可，见 `app/src/main/assets/licenses/`。源码包包含应用源文件、资源、词库、构建配置和脚本；不含发行签名私钥或用户数据。

## 构建应用

1. 安装 JDK 21、Gradle 8.11.1、Android SDK Platform 35 / Build Tools 35.0.0。Android Gradle Plugin 为 8.9.2。
2. 运行 `tools/download-voice-runtime.ps1`，获取 sherpa-onnx 1.12.14 AAR；脚本校验固定 SHA-256。也可按该脚本中的 URL 手动下载到 `app/libs/sherpa-onnx-1.12.14.aar` 并校验。
3. 项目根目录新建 `local.properties`，填写本机 SDK 的 `sdk.dir`。修改 `gradle.properties` 中 `java.io.tmpdir` 和 `user.home` 的 D 盘路径为本机目录，或移除这两个 JVM 参数。
4. 执行 `gradle :app:assembleDebug`，输出 `app/build/outputs/apk/debug/app-debug.apk`。首次需要联网下载 Google Maven / Maven Central 依赖；以后可用 `--offline`。
5. 本项目 D 盘工具链就绪时，可直接运行 `tools/build-prototype.ps1 -WithTests -Lint -Offline`。脚本使用 `D:\AndroidDev`，在其英文目录构建并输出到 `dist`。

开发者自行构建会使用自己的开发签名，不能直接覆盖现有官方测试包。重新构建无需原发行私钥；卸载已有应用会删除记录，请先在独立模拟器验证。

## 原生依赖的对应源码

固定 AAR 的 SHA-256：
`5a629a899888cb2760e245d9d5340858b15591aee7fa13644cf57199ff2829b9`

- sherpa-onnx v1.12.14 完整源码：
  https://github.com/k2-fsa/sherpa-onnx/archive/refs/tags/v1.12.14.zip
- eSpeak NG 固定修订完整源码：
  https://github.com/csukuangfj/espeak-ng/archive/f6fed6c58b5e0998b8e68c6610125e2d07d595a7.zip
- sherpa-onnx 的 `android/`、`scripts/`、`.github/workflows/` 和 `cmake/` 包含 JNI、Android AAR 构建入口及其原生依赖的固定版本和获取规则。使用该标签中的 Android 构建流程及对应 NDK/CMake 配置，保留 TTS 支持，即可构建上游运行库。本应用未修改这些原生源码；APK 仅保留 arm64-v8a、x86_64 的推理与 JNI 动态库。
- Kotlin 2.1.0：https://github.com/JetBrains/kotlin/tree/v2.1.0
- Commons Compress 1.27.1：https://archive.apache.org/dist/commons/compress/source/
- Commons IO 2.16.1：https://archive.apache.org/dist/commons/io/source/
- Commons Lang 3.16.0：https://archive.apache.org/dist/commons/lang/source/
- Commons Codec 1.17.1：https://archive.apache.org/dist/commons/codec/source/

上述公共地址为第三方对应源码的获取入口，无需向本项目提供账号或支付费用。

## 数据和模型

开放词库及索引已经随源码包含。固定数据来源与哈希在 `dictionary-info.json`，重新生成脚本在 `tools/`。

英美语音模型由应用分别下载，URL、文件名、大小和 SHA-256 固定在 `VoiceStore.java`。模型卡和语料许可随源码及 APK 提供。模型不包含在 APK 内，首次下载需联网；下载后合成和播放均在设备完成。完整语音包下载仍需在可访问 GitHub 且证书链有效的网络下验证。
