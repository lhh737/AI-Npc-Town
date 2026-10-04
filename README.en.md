<div align="center">

# 🌆 AI Npc Town

**An AI NPC interaction system built with Godot, FastAPI and LLM Agents**

[![Godot](https://img.shields.io/badge/Godot-4.6-478CBF?style=flat-square&logo=godot&logoColor=white)](https://godotengine.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104-009688?style=flat-square&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Python](https://img.shields.io/badge/Python-3.10+-blue?style=flat-square)](https://www.python.org/)
[![Qdrant](https://img.shields.io/badge/Qdrant-Vector_DB-DC244C?style=flat-square)](https://qdrant.tech/)
[![License](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](./LICENSE)

[中文](README.md) | [English](README.en.md)

> **Status: under active development** — the core system runs; code cleanup and documentation are in progress.

</div>

---

## Introduction

In traditional games, NPCs can only deliver fixed lines, or offer limited interaction through hand-written dialogue trees. Even in complex RPGs, character dialogue is scripted in advance. That approach is controllable, but it lacks any real sense of aliveness.

This project explores the alternative: **what happens when game NPCs are wired to a large language model.** Players converse freely in natural language, and each NPC has its own persona, speaking style and long-term memory — remembering what you said last time, how you two got along, what you prefer — and adjusts its attitude accordingly.

An experiment in giving game NPCs genuine conversational ability by putting an LLM agent inside the game loop. Instead of fixed dialogue trees, each NPC is an agent with its own persona, short- and long-term memory, and an affinity score that evolves through interaction.

### Core Features

| Module | Description |
|--------|-------------|
| **Natural-language dialogue** | Talk to NPCs freely — no fixed options |
| **Memory system** | Short- and long-term memory; relevant history is retrieved before generating a reply |
| **Affinity system** | Attitude evolves: stranger → acquaintance → friend → close → confidant |
| **Personas** | Each NPC has its own job, personality, expertise and speaking style |
| **Full logging** | Every conversation and affinity change is recorded and traceable |

## Architecture

```
┌──────────────────────────────────────────────────────┐
│  Frontend · Godot 4.6                                 │
│  Rendering · player control · NPC display · chat UI    │
└───────────────────────┬──────────────────────────────┘
                        │ HTTP API
                        ▼
┌──────────────────────────────────────────────────────┐
│  Backend · FastAPI                                    │
│  Routing · NPC state · dialogue handling · logging     │
└───────────────────────┬──────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────────────┐
│  Agent layer · HelloAgents                            │
│  Each NPC = one SimpleAgent (own memory + state)       │
│  Memory retrieval → LLM generation → affinity scoring │
└───────────────────────┬──────────────────────────────┘
                        │
                        ▼
┌──────────────────────────────────────────────────────┐
│  External services                                    │
│  LLM API  ·  Qdrant vector store  ·  SQLite            │
└──────────────────────────────────────────────────────┘
```

### Data Flow

```
Player presses E
   → Godot sends the dialogue request (HTTP)
   → FastAPI routes to the right NPC
   → SimpleAgent retrieves relevant history from memory
   → Calls the LLM to generate a reply
   → Updates NPC state and affinity
   → Writes logs (console + file)
   → Returns the reply to Godot
   → UI updates — one interaction cycle complete
```

## NPC Characters

| NPC | Role | Personality | Speaking style |
|-----|------|------------|----------------|
| **Zhang San** | Python engineer | Enthusiast, enjoys discussing algorithms and frameworks | Concise and professional, likes technical terms, occasionally vents about bugs |
| **Li Si** | Product manager | Outgoing and talkative, good at coordination | Friendly and warm, explains with analogies |
| **Wang Wu** | UI designer | Sensitive and detail-oriented | Cares about layering and presentation |

> Every NPC's settings (role / location / activity / personality / expertise / style / hobbies) are structured configuration, injected into the agent's role prompt at generation time.

## Demo

<div align="center">
  <img src="docs/demo1.png" width="700" alt="Game scene">
  <br>
  <em>Figure 1 · Pixel-art office scene — move with WASD, an interaction hint appears near an NPC</em>
</div>

<br>

<div align="center">
  <img src="docs/demo2.png" width="700" alt="NPC dialogue UI">
  <br>
  <em>Figure 2 · Natural-language dialogue with an NPC</em>
</div>

## Project Structure

```
AI-Npc-Town/
├── helloagents-ai-town/           # Godot project
│   ├── project.godot              #   Godot project config
│   ├── scenes/                    #   Scenes
│   │   ├── main.tscn              #     Main scene (office)
│   │   ├── player.tscn            #     Player character
│   │   ├── npc.tscn               #     NPC character
│   │   └── dialogue_ui.tscn       #     Dialogue UI
│   ├── scripts/                   # GDScript
│   │   ├── main.gd                #     Main scene logic
│   │   ├── player.gd              #     Player control
│   │   ├── npc.gd                 #     NPC behaviour
│   │   ├── dialogue_ui.gd         #     Dialogue UI logic
│   │   ├── api_client.gd          #     API client
│   │   └── config.gd              #     Configuration
│   └── assets/                    #   Game assets
│       ├── characters/            #     Character sprites
│       ├── interiors/             #     Interior scenes
│       ├── ui/                    #     UI assets
│       └── audio/                 #     Sound and music
│
├── backend/                       # Python backend
│   ├── main.py                    #   FastAPI application
│   ├── agents.py                  #   NPC agent system (personas + memory)
│   ├── relationship_manager.py    #   Affinity management
│   ├── state_manager.py           #   NPC state management
│   ├── .env.example               #   Environment variable template
│   ├── models.py                  #   Data models
│   ├── logger.py                  #   Logging
│   ├── config.py                  #   Configuration
│   ├── view_logs.py               #   Log viewer
│   ├── batch_generator.py         #   Batch data generation
│   └── requirements.txt           #   Python dependencies
│
└── docs/                          # Documentation
    ├── MEMORY_SYSTEM_GUIDE.md     #   Memory system design
    ├── AFFINITY_SYSTEM_GUIDE.md   #   Affinity system design
    ├── DIALOGUE_LOG_GUIDE.md      #   Dialogue logging conventions
    └── SETUP_GUIDE.md             #   Environment setup
```

## Setup

### Requirements

- **Godot** ≥ 4.6 — [download](https://godotengine.org/download)
- **Python** ≥ 3.10
- **An LLM API key** — configured in `.env`

### 1. Start the backend

```bash
cd backend
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
uvicorn main:app --reload
```

### 2. Configure the API key

Copy the template and fill in your LLM API key:

```bash
cp backend/.env.example backend/.env
# edit backend/.env, set LLM_API_KEY and related settings
```

### 3. Run the game

Open `helloagents-ai-town/project.godot` in Godot and run the main scene.

### 4. Controls

| Key | Action |
|-----|--------|
| `W A S D` | Move |
| `E` | Interact with a nearby NPC |

> See `docs/SETUP_GUIDE.md` for the full walkthrough.

## Affinity System

Affinity is implemented on the backend — each conversation adjusts the score based on the content and sentiment of the player's message.

The score is not shown directly in the game UI, but every change is logged to `backend/logs/dialogue_YYYY-MM-DD.log`:

| Log field | Description |
|-----------|-------------|
| Current affinity | The NPC's current relationship stage with the player |
| Retrieved memories | History consulted while generating the reply |
| NPC reply | The generated response |
| Affinity delta | e.g. `+2.0`, `+3.0` |
| Reason | Friendly greeting, normal conversation, … |
| Sentiment analysis | `positive` / `neutral` / … |

> Detailed design in `docs/AFFINITY_SYSTEM_GUIDE.md`

## Roadmap

- [x] Godot scene and character control
- [x] FastAPI backend and NPC agent system
- [x] Memory system (short + long term)
- [x] Affinity system with logging
- [ ] Code cleanup and modular refactor
- [ ] Affinity visualisation in the frontend
- [ ] More NPCs and scene expansion
- [ ] Retrieval strategy tuning

## Discussion

AI NPCs show what LLMs can bring to games, but three real constraints remain:

- **Cost** — every turn calls the LLM API, which gets expensive in a large multiplayer game
- **Latency** — inference takes time; on a slow connection players wait several seconds
- **Control** — generated content is not fully predictable, so prompts and filtering are needed

Faster inference and capable small local models are easing these constraints. Combining AI with games opens interaction patterns that scripted dialogue trees simply cannot express.

## License

MIT © [lhh737](https://github.com/lhh737)

## Credits

Art assets from [Datawhale](https://github.com/datawhalechina); the agent framework is built on [Hello-Agents](https://github.com/datawhalechina/Hello-Agents).