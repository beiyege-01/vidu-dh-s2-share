> ## 🧪 这是「试验版 / 前瞻功能」仓库
>
> 这里放的是**正在试验的新功能**：可能不稳定、随时会改、不保证跑通。
> **稳定版 / 正式版请用 👉 [cyber-xiaoya / cyber-xiaoya-live](https://github.com/cyber-xiaoya/cyber-xiaoya-live)**
>
> | | 本仓库（试验版） | [正式版仓库](https://github.com/cyber-xiaoya/cyber-xiaoya-live) |
> |---|---|---|
> | 内容 | 前瞻功能、还没经过长时间实播验证的改法 | 已实播验证的稳定版本 |
> | 适合 | 尝鲜、看实现思路、提建议 | **真正开播就用这个** |
> | 文档 | 可能滞后 | 完整（安装 / 配置 / 常见问题） |
> | 反馈 | 功能讨论可以在本仓库提 | 使用问题请到那边提 |
>
> 两个仓库功能会**周期性同步**：试验版验证通过 → 合并进正式版。
>
> 📜 **开源协议与本仓库一致**：代码 [GNU AGPL-3.0](./LICENSE)，素材/品牌见 [NOTICE](./NOTICE)，
> **永久免费、不卖课、不卖安装服务**（详见文末「许可证」「关于倒卖」）。

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

## 许可证

- **代码**：[GNU AGPL-3.0](./LICENSE) —— 与本项目正式版 [cyber-xiaoya-live](https://github.com/cyber-xiaoya/cyber-xiaoya-live) 同一授权
- **素材与品牌**：见 [NOTICE](./NOTICE)，**不在 AGPL 授权范围内**
- 第三方依赖见 `requirements.txt`

> 简单说：**改代码、自己用、二次开发都行，但改了要照样开源（AGPL）**；
> 人物形象、克隆音色、人设、歌单、动作库这些素材不随代码授权，别拿去商用或倒卖。

## 关于倒卖

本项目**代码免费、永久免费**，我们不会在任何平台出售它。

> ⚠️ **本项目没有官方 QQ 群、微信群，也不卖课、不卖安装服务。**
> 任何以「官方答疑群」「内部版」「授权版」「代部署」名义收费的，都不是我，请直接拉黑。

如果你看到有人在卖，那不是官方行为，欢迎把链接发到
[正式版仓库的 Issue](https://github.com/cyber-xiaoya/cyber-xiaoya-live/issues)。
也提醒买家：盗版没有更新、没有答疑、出了问题也没人负责。

## 关注作者

赛博小雅是我（**天工开帧**）在业余时间做的开源项目。想看她直播、催更、或者聊聊实现细节，来这些地方找我：

| 平台 | 链接 |
|---|---|
| 🎵 抖音 | <https://v.douyin.com/BgCJXnllitM/> |
| 📺 哔哩哔哩 | <https://space.bilibili.com/11946287> |
| ▶️ YouTube | <https://www.youtube.com/@jintaoyi> |
| ✖️ X / Twitter | <https://x.com/yijt1339303> |
| 📝 博客 · 教程 | <https://www.19980303.xyz/> |
| 💻 GitHub | <https://github.com/beiyege-01> |

> **使用问题**请提到正式版仓库的 [Issue](https://github.com/cyber-xiaoya/cyber-xiaoya-live/issues)（公开可查，后来人也能搜到）；
> **本仓库（试验版）的 Issue** 只适合讨论前瞻功能的思路。
> 想直接看效果：抖音直播间搜「天工开帧」，晚上常有直播。
