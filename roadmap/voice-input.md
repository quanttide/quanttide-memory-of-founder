# 语音输入工具路线图

## 目标

建立稳定可靠的中文语音输入链路，满足日常写作、编程注释、文档编辑等场景下的高效输入需求。

---

## 当前状态

### 链路状态

```
快捷键 IPC → 录音 → whisper 转录 → ydotool 键入
    ✅         ⚠️        ✅           ✅
```

全链路代码逻辑已调通。**瓶颈在录音环节**：麦克风硬件信号过弱，whisper 无法从中提取有效语音。

### 已完成的排查与修复

| 领域 | 事项 | 状态 |
|------|------|------|
| 链路 | IPC 通信调通（socket 自动重建） | ✅ |
| 链路 | AIPP 阻塞排除（关闭后链路不再卡死） | ✅ |
| 音频 | ALSA Input Source 从 Rear Mic 切到 Front Mic | ✅ |
| 音频 | Front Mic Boost 从 0dB 设为 30dB | ✅ |
| 音频 | Capture 增益设为最大 30dB | ✅ |
| 音频 | PulseAudio 音量锁定 100% | ✅ |
| 音频 | 噪声门控/谱减法/anlmdn 降噪测试（均无效） | ❌ |
| 模型 | 从 ggml-base (142MB) 升级到 ggml-small (466MB) | ✅ |
| 硬件 | 前麦克风接口信号弱（RMS 600-1500, 需 5000+） | ❌ 瓶颈 |

---

## 路线图

### 阶段 1：提升麦克风信号质量（硬件方向）

当前核心瓶颈是信噪比过低（~6dB），软件无法弥补。需要从硬件侧解决。

| 优先级 | 方案 | 预期效果 | 成本 |
|--------|------|---------|------|
| P0 | **耳机麦克风移到嘴边 1-2cm** | 近场效应可提升信号 10-20dB，信噪比达 20-30dB | 0 |
| P1 | **USB 麦克风**（~¥50 会议麦） | 独立 ADC，远离机箱电磁干扰 | ¥50+ |
| P2 | **机箱移到桌下** | 拉远风扇噪声源 | 0 |
| P3 | **蓝牙耳机连电脑** | 麦克风贴近嘴，蓝牙远离机箱 | 已有即可 |

### 阶段 2：备选方案验证

若硬件改善后仍有识别准确率问题：

| 任务 | 说明 |
|------|------|
| 下载 Dabri Flatpak | 内置 small 模型，可能对低 SNR 更鲁棒 |
| 纯脚本方案 | `arecord → whisper-cli → ydotool type` 最小链路 |

### 阶段 3：软件调优（信号改善后执行）

| 任务 | 说明 |
|------|------|
| `audio_prefer_pulse: false` | 避免 sounddevice 匹配"pulse"多设备异常路径 |
| 测试 whisper 参数 | temperature, beam size, VAD threshold |
| 集成 RNNoise 后处理 | `pip install noisereduce` 做转录前降噪 |
| 考虑 medium 模型 | 若 small 仍不够, 升级 `ggml-medium.bin` (1.5GB) |

### 阶段 4：工作流完善

| 任务 | 说明 |
|------|------|
| 启用 AIPP | 切换到 ollama 本地 LLM 做后处理 |
| 建立写作工作流 | 语音输入 → AIPP 润色 → 输出到编辑器 |

---

## 元目标

以语音输入工具的落地过程，验证本地 AI 工具在 Linux 桌面环境（Wayland）中的集成策略与调试方法论。

已验证的技术点：
- **IPC 通信**：Unix socket 双向通信（client → tray server → toggle_recording）
- **PyQt6 tray 集成**：系统托盘 + 回调 + Qt 线程安全
- **whisper.cpp 部署**：whisper-cli 命令行接口 + 多语言模型切换
- **ydotool 键盘模拟**：Wayland 下模拟键入
- **音频链路**：ALSA → PulseAudio → sounddevice → VAD → 降噪
- **ALSA 配置**：Input Source / Mic Boost / Capture 增益调优与持久化
