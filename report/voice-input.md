# 语音输入法安装状态报告

## VOXD（jakovius/voxd）

### 已实现

- 通过 .deb 包安装 v1.7.0
- Python 依赖已解决（pyyaml, platformdirs, PyQt6 等）
- whisper-cli 已通过 apt 安装（whisper.cpp v1.8.3）
- 多语言模型 ggml-base.bin（142MB）已从 hf-mirror.com 下载
- 配置：language=zh, 开机自启已启用, 详细日志已开启
- 全局快捷键已设置：Win+Z → `bash -c 'voxd --trigger-record'`
- ydotoold 服务正常运行，可模拟中文键盘输入
- 用户已加入 input 组

### 差距

- 核心识别+打字链路已验证通过（whisper-cli + ydotool 各自测试正常）
- 但用户按压快捷键后无法返回中文转录结果，具体原因待排查：
  1. 配置文件可能被 AppConfig.load() 覆盖
  2. whisper.cpp v1.8.3（apt 版）与 ggml-base.bin 的兼容性
  3. 热键 IPC 触发链路（voxd --trigger-record → tray.sock）需验证

## Dabri（原 Speak-to-AI, AshBuk/dabri）

### 已实现

- 确认支持 Linux（AppImage/Flatpak 两种便携格式）
- 确认支持 Wayland
- 确认支持中文（Whisper 多语言模型）
- Flatpak 版已内置 small 模型，开箱即用

### 差距

- AppImage（321MB）/ Flatpak（192MB）需从 GitHub Releases 下载
- 国内网络环境下载慢，尚未成功下载
