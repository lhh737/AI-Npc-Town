# 🌆构建赛博小镇

智能体技术与游戏引擎的结合，赛博小镇包含以下核心功能：

<strong>（1）智能 NPC 对话系统</strong>：玩家可以与 NPC 进行自然语言对话，NPC 会根据自己的角色设定和记忆做出回应。

<strong>（2）记忆系统</strong>：NPC 拥有短期记忆和长期记忆，能够记住与玩家的互动历史。

<strong>（3）好感度系统</strong>：NPC 对玩家的态度会随着互动而变化，从陌生到熟悉，从友好到亲密。

<strong>（4）游戏化交互</strong>：玩家可以在 2D 像素风格的办公室场景中自由移动，与不同的 NPC 互动。

<strong>（5）实时日志系统</strong>：所有对话和互动都会被记录，方便调试和分析。

## 📦项目概述与架构设计

### 为什么要构建 AI 小镇

传统游戏中的 NPC 通常只能说固定的台词，或者通过预设的对话树进行有限的互动。即使是最复杂的 RPG 游戏，NPC 的对话也是由编剧事先写好的。这种方式虽然可控，但缺乏真正的"智能"和"生命力"。

想象一下，如果游戏中的 NPC 能够理解你说的任何话，不再局限于预设的选项，你可以用自然语言与 NPC 交流。NPC 会记得你上次说了什么，你们的关系如何，甚至你的喜好。每个 NPC 都有自己的职业、性格和说话风格。NPC 对你的态度会随着互动而变化，从陌生人到朋友，甚至挚友。

这就是 AI 技术为游戏带来的新可能。通过将大语言模型与游戏引擎结合，我们可以创造出真正"活着"的 NPC。这不仅仅是一个技术演示，更是对未来游戏形态的探索。在教育游戏中，NPC 可以扮演历史人物、科学家，与学生进行互动式教学。在虚拟办公室中，NPC 可以扮演同事、导师，提供帮助和建议。NPC 还可以作为陪伴者，与用户进行情感交流，应用于心理健康领域。当然，最直接的应用就是为传统游戏增加 AI NPC，提升玩家体验。

### 技术架构概览

赛博小镇采用<strong>游戏引擎+后端服务</strong>的分离架构，分为四个层次，如下图所示。

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-1.png" alt="" width="85%"/>
  <p>图 1 赛博小镇技术架构</p>
</div>

前端层使用 Godot 4.6 游戏引擎，负责游戏渲染、玩家控制、NPC 显示和对话 UI。Godot 是一个开源的 2D/3D 游戏引擎，非常适合快速开发像素风格的游戏。后端层使用 FastAPI 框架，负责 API 路由、NPC 状态管理、对话处理和日志记录。FastAPI 是一个现代化的 Python Web 框架，性能优秀且易于开发。智能体层使用开源的 HelloAgents 框架，负责 NPC 智能、记忆管理和好感度计算。每个 NPC 都是一个 SimpleAgent 实例，拥有独立的记忆和状态。外部服务层提供 LLM 能力、向量存储和数据持久化，包括 LLM API、Qdrant 向量数据库和 SQLite 关系数据库。

数据流转过程如下图所示：

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-2.png" alt="" width="85%"/>
  <p>图 2 数据流转过程</p>
</div>


玩家在 Godot 中按 E 键与 NPC 互动，Godot 通过 HTTP API 发送对话请求到 FastAPI 后端。后端调用 HelloAgents 的 SimpleAgent 处理对话，Agent 从记忆系统中检索相关历史，然后调用 LLM 生成回复。后端更新 NPC 状态和好感度，记录日志到控制台和文件，最后返回回复给 Godot 前端。Godot 显示 NPC 回复并更新 UI，完成一次完整的交互循环。

项目的结构如下:

```
Helloagents-AI-Town/
├── helloagents-ai-town/           # Godot游戏项目
│   ├── project.godot              # Godot项目配置
│   ├── scenes/                    # 游戏场景
│   │   ├── main.tscn              # 主场景(办公室)
│   │   ├── player.tscn            # 玩家角色
│   │   ├── npc.tscn               # NPC角色
│   │   └── dialogue_ui.tscn       # 对话UI
│   ├── scripts/                   # GDScript脚本
│   │   ├── main.gd                # 主场景逻辑
│   │   ├── player.gd              # 玩家控制
│   │   ├── npc.gd                 # NPC行为
│   │   ├── dialogue_ui.gd         # 对话UI逻辑
│   │   ├── api_client.gd          # API客户端
│   │   └── config.gd              # 配置管理
│   └── assets/                    # 游戏资源
│       ├── characters/            # 角色精灵图
│       ├── interiors/             # 室内场景
│       ├── ui/                    # UI素材
│       └── audio/                 # 音效音乐
│
└── backend/                       # Python后端
    ├── main.py                    # FastAPI主程序
    ├── agents.py                  # NPC Agent系统
    ├── relationship_manager.py    # 好感度管理
    ├── state_manager.py           # 状态管理
    ├── logger.py                  # 日志系统
    ├── config.py                  # 配置管理
    ├── models.py                  # 数据模型
    ├── requirements.txt           # Python依赖
    └── .env                       # 环境变量
```

详细的架构设计和数据流转将在后续的补充中介绍。

## 🎮效果预览
游戏启动后，你会看到一个像素风格的 Datawhale 办公室场景，美术等相关资源包是从Datawhale获取的，如下图所示。

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-3.png" alt="" width="85%"/>
  <p>图 3 赛博小镇游戏场景</p>
</div>

使用 WASD 键移动玩家角色，走到 NPC 附近时，屏幕上会显示"按 E 键交互"的提示。按下 E 键后，会弹出对话框，你可以输入任何想说的话，如图所示。

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-4.png" alt="" width="85%"/>
  <p>图 4 与 NPC 对话界面</p>
</div>

NPC 会根据自己的角色设定(Python 工程师、产品经理、UI 设计师)和你们的互动历史做出回应。随着对话的进行，NPC 对你的好感度会逐渐提升，从"陌生"到"熟悉"，再到"友好"、"亲密"甚至"挚友"。

<strong>好感度系统在后端实现</strong>，每次对话都会根据玩家的消息内容和情感分析来调整好感度值。虽然前端游戏界面中没有直接显示好感度数值，但所有的好感度变化都会被详细记录在后端日志中。你可以在`backend/logs/dialogue_YYYY-MM-DD.log`文件中查看每次对话的好感度变化。日志文件会记录每次对话的详细信息，包括：当前好感度值、检索到的相关记忆、NPC 的回复、好感度变化量(+2.0、+3.0 等)、变化原因(友好问候、正常交流等)以及情感分析结果(positive、neutral 等)。这种设计让开发者可以清晰地追踪 NPC 与玩家的关系发展，也为后续在前端添加好感度 UI 提供了数据基础。


## 总结与展望

在本项目中，我们完成了一个完整的 AI 小镇项目——赛博小镇。这个项目将 HelloAgents 框架与 Godot 游戏引擎结合，创造出了一个充满生命力的虚拟世界。

整个项目的技术栈如下图所示：

<div align="center">
  <img src="https://raw.githubusercontent.com/datawhalechina/Hello-Agents/main/docs/images/15-figures/15-15.png" alt="" width="85%"/>
  <p>图 5 赛博小镇技术栈</p>
</div>



### 🎬 思考与展望

赛博小镇展示了 AI 技术在游戏中的巨大潜力。传统游戏中的 NPC 受限于预设的对话树和脚本，而 AI NPC 可以理解和生成自然语言，与玩家进行真正的对话。这不仅提升了游戏的沉浸感，也为游戏设计带来了新的可能性。

但 AI NPC 也面临一些挑战。首先是成本问题，每次对话都需要调用 LLM API，这会产生一定的费用。对于大型多人在线游戏，这个成本可能会很高。其次是延迟问题，LLM 的推理需要时间，如果网络延迟较高，玩家可能需要等待几秒才能看到 NPC 的回复。最后是内容控制问题，LLM 生成的内容可能不完全可控，需要设计好的提示词和内容过滤机制。

尽管有这些挑战，AI NPC 的未来仍然充满希望。随着 LLM 技术的发展，推理速度会越来越快，成本会越来越低。本地化的小型 LLM 也在快速发展，未来可能可以在玩家的设备上直接运行，完全不需要网络请求。AI 技术与游戏的结合，将为玩家带来前所未有的体验。

