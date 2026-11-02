> ## ⚠️ 这个仓库已经停止更新，请去新的官方公开库 👇
>
> # 👉 **[cyber-xiaoya / cyber-xiaoya-live](https://github.com/cyber-xiaoya/cyber-xiaoya-live)**
>
> 本仓库只是发布过程中的**临时副本**（内容定格在 2026-11-02 的快照）。
> **功能更新、安装文档、Issue、版本发布全部在 [cyber-xiaoya-live](https://github.com/cyber-xiaoya/cyber-xiaoya-live)**，
> 请不要在这个仓库提 Issue 或下载，那边才是最新、最完整的版本（AGPL-3.0，作者：天工开帧）。

---

# 云端数字人 · 组件版（Vidu s2）

> 只做「渲染 + 推流」的数字人外壳：**画面与口型交给 Vidu s2，声音、大模型、弹幕、放歌全在你自己的机器上跑。**
> 一个 FastAPI 桥（`:8795`）+ 一个舞台页（`/stage`）+ 一套弹幕/动作指令路由。

## 它长这样

- 左边是数字人画面（Vidu 实时渲染推流）；右边是「字幕 / 事件」
- 底部：打字聊天、直发动作、打断、点歌单、快捷指令、口播
- 弹幕进来 → 匹配动作指令 → 直接让她做动作；说的话走「LLM → TTS → 音频推流」
- 放歌时歌词同步显示，说话会让位、歌完自动回来

## 能力一览

| 模块 | 说明 |
|---|---|
| 说话链 | LLM（可换 DeepSeek/任意 OpenAI 兼容）→ OmniVoice/系统 TTS → PCM 喂给 Vidu |
| 动作链 | type9 指令直发，不经 LLM；带优先级队列（手动 > 编排 > 弹幕） |
| 弹幕链 | 抖音/B站/视频号读取 → 指令路由器（可配置别名、黑名单、静默阈值） |
| 歌曲链 | 歌单 + 歌词对齐 + 歌中静默 + 播完回到聊天 |
| 快捷指令 | 分组动作面板（吃喝/外表/拟人化/手势/拿出），可自定义隐藏与排序 |
| 固定编排 | 多组动作随机轮播，组内 2s / 组间 15s（可调） |
| 专属会员 | 名单里的观众进场 → 主动喊一句欢迎（歌中排队不打断） |
| 统计面板 | 本场 token / Vidu 积分 / 礼物钻石 / 记忆轮数，可鼠标拖动 |

## 快速开始

```powershell
# 1) 依赖
python -m venv .venv
.venv\Scripts\pip install -r requirements.txt

# 2) 配置（照 config.example.json 改成 config.json，填你自己的密钥）
copy config.example.json config.json

# 3) 启动桥 + 打开舞台页
powershell -ExecutionPolicy Bypass -File start-component-stack.ps1
# 浏览器打开 http://127.0.0.1:8795/stage
```

配置里的关键项：

| 项 | 填什么 |
|---|---|
| `vidu.api_key` | 你的 Vidu API Key |
| `vidu.avatar_id` | 你的数字人形象 ID |
| `rtc.*` | 平台托管 RTC 时留空即可；自备 RTC 才需要 app_key/secret |
| `llm.*` | 你的 LLM 服务（默认 DeepSeek，`key_file` 指向存 key 的文本文件） |
| `tts.*` | 音色与歌单目录 |

## 目录结构

```
src/            FastAPI 桥、引擎、LLM、TTS、弹幕路由、Vidu 客户端
stage/          舞台页（单文件 HTML）
personas/       人设（示例两份，可自行改）
data/           运行数据（会员名单、编排；示例为空）
tools/          自检与调试脚本（selfcheck / stage_verify / 弹幕回放 …）
```

## 注意

- **密钥不入库**：`config.json`、`key.txt` 都在 `.gitignore` 里
- **平台合规**：动作/台词黑名单在 `tools/dm-filter.json`，说话侧敏感词在 `config.danmaku.speech_block_words`
- **语言模型合规**：人设里明确避开政治/色情/暴力；被问隐私会岔开
- 本项目只负责"编排 + 推流"，不含 Vidu s2 的账号与计费

## License

仅供学习与个人直播使用。使用 Vidu 服务需遵守其平台条款。
