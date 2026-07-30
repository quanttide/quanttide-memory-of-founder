# 语音输入法安装状态报告

## VOXD（jakovius/voxd）

### 安装

- v1.7.0, 通过 .deb 包安装, 路径 `/opt/voxd/src/voxd/`
- Wrapper: `/usr/bin/voxd` → sets `PYTHONPATH=/opt/voxd/src` → `python3 -m voxd`
- 使用系统 Python (`/usr/bin/python3`), 而非独立 venv
- systemd user 服务 `voxd-tray.service` 已启用, Restart=on-failure

### 已修复的问题

| 问题 | 原因 | 处理 |
|------|------|------|
| AIPP 阻塞链路 | `aipp_enabled: true` 但 `llama-server` 未运行, 导致 `get_final_text()` 30s 超时, 整条链路卡死 | `aipp_enabled: false` |
| IPC socket 失效 | 旧 tray 进程退出后 socket 文件残留, `voxd --trigger-record` 连接被拒绝 (Errno 111) | 重启 tray 服务, socket 自动重建 |
| 输入源指向 Rear Mic | ALSA `Input Source` 默认为 `Rear Mic`, 但后面板未插麦克风, 信号极弱 | 切换到 `Front Mic` 并持久化 |
| 前麦克风增益为 0 | `Front Mic Boost` 为 0 (0dB), Realtek ALC897 前麦克风前置放大器未启用 | 设为 3 (30dB) |
| Capture 增益不足 | `Capture` 通道增益不完整, 信号弱 | 设为 63/63 (30dB, 最大) |
| 模型过小 | `ggml-base.bin` (142MB) 中文识别精度低, 对噪声敏感 | 升级到 `ggml-small.bin` (466MB) |
| 麦克风音量不稳定 | PulseAudio 音量被 VOXD 的 `mic_autoset_level: 0.45` 自动设为 45% | 改为 1.0 (100%), ALSA 增益固定 |

### 正常工作链路

```
快捷键 Super+Z → GNOME keybinding → bash -c 'voxd --trigger-record'
    → IPC client → Unix socket (~/.config/voxd/voxd.sock)
    → tray IPC server (tray_main.py) → toggle_recording()
    → CoreProcessThread → AudioRecorder (sounddevice / PulseAudio)
    → 录音至 /tmp/voxd_temp/chunk_*.wav → 缝合
    → WhisperTranscriber (whisper-cli + ggml-small.bin)
    → SimulatedTyper (ydotool type) → 键入目标窗口
```

### 当前瓶颈：麦克风硬件信号弱

即使 ALSA 增益已最大化（Front Mic Boost 30dB + Capture 30dB = 60dB），信号仍然很弱：

| 指标 | 数值 | 正常参考 |
|------|------|---------|
| 语音 RMS | 600-1500 | 5000-30000 |
| 底噪 RMS（风扇） | 200-400 | <100 |
| 信噪比 | ~6dB | >20dB |
| 削波 | 0%（100% 音量时正常） | 无害 <1% |

**表现**：whisper 无法从噪声中分辨语音，输出均为模型幻觉：
- "字幕製作:貝爾"
- "作詞:李宗盛"
- "未经许可,不得翻唱或使用"

**软件降噪无效**的原因：风扇噪声为宽带噪声（频率与语音重叠），且语音能量远低于噪声门限（RMS 600 vs 需 5000+），FFT 谱减法/Wiener 滤波/anlmdn 均无法恢复有效信号。

### 当前 ALSA 配置（已固化）

```
Input Source:   Front Mic
Front Mic:     Playback 0% (monitor off)
Front Mic Boost: 3 (100%, 30dB)
Capture:        63/63 (100%, 30dB, on)
```

PulseAudio 源音量: 100%, 无削波

### VOXD 当前配置

```yaml
aipp_enabled: false
language: zh
whisper_model_path: /home/iguo/.local/share/voxd/models/ggml-small.bin
mic_autoset_level: 1.0
verbosity: true
typing: true
```

### 关键文件路径

| 文件 | 路径 |
|------|------|
| 配置文件 | `~/.config/voxd/config.yaml` |
| IPC socket | `~/.config/voxd/voxd.sock` |
| 模型文件 | `~/.local/share/voxd/models/ggml-small.bin` |
| 录音缓存 | `/tmp/voxd_temp/` |
| ydotool socket | `~/.ydotool_socket` |
| 日志 | `journalctl --user -u voxd-tray` |

## Dabri（原 Speak-to-AI, AshBuk/dabri）

### 确认信息

- 支持 Linux（AppImage/Flatpak）
- 支持 Wayland
- 支持中文（Whisper 多语言模型）
- Flatpak 版已内置 small 模型，开箱即用

### 状态

- ❌ 尚未下载（国内网络慢）

### 潜在价值

Dabri 使用内置 small 模型 + 可能不同的音频处理链，对本机低 SNR 环境的容忍度可能高于 VOXD，值得作为备选验证。
