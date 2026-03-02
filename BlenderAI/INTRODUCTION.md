# Blender AI — Turn Words into Short Films

> **Describe a scene. Get a rendered animation.**
> No 3D modelling experience required.

> **Repository status:** The Blender AI source code is being published as a standalone GitHub repository.
> **Coming soon** — watch this page for updates.

---

## What Is This?

Blender AI is an open-source pipeline that connects Claude (Anthropic's AI) to Blender, the world's leading free 3D creation suite. You write a sentence — or a paragraph — describing what you want to see, and the tool handles everything else: breaking your idea into shots, generating the 3D scene, animating it, and delivering a finished MP4.

It is not a game engine. It is not a video editor. It is a **creative co-pilot** that speaks Blender fluently so you don't have to.

---

## Who Is It For?

| You are… | Blender AI helps you… |
|---|---|
| A **graphic designer** exploring motion | Prototype animated concepts in minutes, not weeks |
| A **storyboard artist** | Render rough 3D animatics straight from your shot notes |
| A **product designer** | Visualise how a product moves, spins, or interacts with space |
| A **creative director** | Pitch motion concepts to clients without commissioning a 3D artist |
| A **filmmaker** | Pre-visualise a sequence before committing budget to production |
| **Curious about 3D** | Experiment freely — no tutorials, no topology headaches |

If you can describe what you want to see, you can make it.

---

## What Does the Output Look Like?

A typical run on the prompt *"A glowing red sphere bouncing on a reflective silver floor under warm golden light"* produced this automatically — no human touched Blender:

```
Storyboard: 6 shots, 22 seconds total
  Shot 1 — Opening Wide         (2.0 s)  camera: static, wide angle
  Shot 2 — First Bounce Drop    (3.0 s)  camera: low dolly
  Shot 3 — Close-Up Bounce Loop (4.0 s)  camera: tight, tracking
  Shot 4 — Orbit Reveal         (4.5 s)  camera: orbiting
  Shot 5 — Final Rest           (4.0 s)  camera: slow pull-back
  Shot 6 — Pull Back to Black   (4.5 s)  camera: crane up
```

Each shot is independently scripted, rendered in CYCLES (Blender's photorealistic engine), and assembled into a single MP4. Start to finish in under 30 minutes on a standard laptop CPU.

---

## How It Works

The pipeline has four stages, all automated:

```
Your Prompt
    │
    ▼
┌───────────────────────────────────┐
│  1. STORYBOARD                    │
│  Claude reads your prompt and     │
│  breaks it into a sequence of     │
│  cinematic shots with camera      │
│  direction and lighting notes.    │
└───────────────┬───────────────────┘
                │
                ▼
┌───────────────────────────────────┐
│  2. SCRIPT GENERATION             │
│  For each shot, Claude writes a   │
│  complete Blender Python script   │
│  — objects, materials, lights,    │
│  camera, keyframe animation.      │
└───────────────┬───────────────────┘
                │
                ▼
┌───────────────────────────────────┐
│  3. RENDER                        │
│  Blender 5.0 runs headlessly and  │
│  renders every frame. Errors are  │
│  caught and automatically fixed   │
│  by Claude (up to 3 retries).     │
└───────────────┬───────────────────┘
                │
                ▼
┌───────────────────────────────────┐
│  4. ASSEMBLY                      │
│  ffmpeg stitches all shots into   │
│  a single MP4 file.               │
└───────────────────────────────────┘
```

No manual steps. No Blender UI. No Python knowledge needed.

---

## Craft Your Prompt Like a Director

The richer your prompt, the more intentional the result. Think in terms of **subject**, **mood**, **movement**, and **setting**.

**Minimal prompt** (still works):
```
A spinning bronze gear on a dark surface
```

**Director-style prompt** (better results):
```
A heavy bronze gear slowly spinning on a worn stone table,
lit by a single overhead lamp casting dramatic shadows.
The camera begins wide, then slowly pushes in to reveal
the intricate teeth of the gear. Industrial, moody atmosphere.
```

**Product visualisation prompt**:
```
A matte black perfume bottle on a rotating glass platform,
surrounded by soft studio lighting. The camera orbits
360 degrees revealing every angle. Clean, luxurious, minimal.
```

**Abstract / motion graphics prompt**:
```
Dozens of silver spheres falling from above and settling
into a pyramid formation, each bounce sending ripples of
light across a mirrored floor. Slow motion feel.
```

---

## Key Features

- **Natural language input** — describe your scene in plain English
- **Automatic storyboarding** — Claude directs the shots for you
- **Full Blender control** — materials, lighting, cameras, keyframe animation
- **Error self-correction** — broken scripts are automatically fixed and re-run
- **Optional visual feedback** — Claude inspects each rendered frame and refines if needed
- **GPU-accelerated rendering** — automatically uses NVIDIA OptiX or CUDA when available
- **Cross-platform** — runs on Windows (via PowerShell), Linux, and macOS
- **Fully open-source** — inspect, extend, and adapt every part of the pipeline

---

## Getting Started

### Prerequisites

| Tool | Purpose | Install |
|---|---|---|
| Python 3.10+ | Runs the pipeline | python.org |
| Blender 4.x / 5.x | 3D rendering | blender.org |
| ffmpeg | Video assembly | ffmpeg.org or `winget install Gyan.FFmpeg` |
| Anthropic API key | Powers the AI | console.anthropic.com |

### Install

> **The repository is coming soon.** Once published, install with:

```bash
git clone https://github.com/haroldmei/blender-ai  # coming soon
cd blender-ai
pip install -r requirements.txt
cp .env.example .env
# Add your ANTHROPIC_API_KEY to .env
```

### Run

```bash
python run.py "Your creative prompt here"
```

The finished video appears at `./output/movie.mp4`.

### Options

```
--output PATH      Where to save frames and the final movie  (default: ./output)
--engine ENGINE    CYCLES (photorealistic) or BLENDER_EEVEE_NEXT (fast preview)
--width  INT       Frame width in pixels                     (default: 854)
--height INT       Frame height in pixels                    (default: 480)
--fps    INT       Frames per second                         (default: 24)
--dry-run          Generate scripts only, skip rendering
--no-vision        Skip Claude's visual quality check
--blender PATH     Path to Blender if not in system PATH
```

### Full Example (Windows)

```powershell
$env:ANTHROPIC_API_KEY = "sk-ant-..."
python run.py "A futuristic city skyline at night with flying cars" `
    --output C:\Projects\city_animation `
    --engine CYCLES `
    --width 1280 --height 720
```

---

## Understanding the Output Folder

After a run, your output directory contains:

```
output/
├── storyboard.json          ← Shot list with descriptions and timing
├── movie.mp4                ← The final assembled animation
├── shot_01/
│   ├── scene.py             ← The generated Blender script
│   └── frames/
│       ├── frame_0001.png
│       ├── frame_0002.png
│       └── ...
├── shot_02/
│   └── ...
└── ...
```

You can open any `scene.py` in Blender's scripting editor to inspect, learn from, or further edit the generated scene. Every script is readable, well-structured Python — a great way to learn Blender scripting by example.

---

## Extending the Pipeline

Because every stage is modular, you can adapt the tool to your workflow:

- **Swap the storyboard** — feed your own shot list as JSON instead of generating one
- **Edit the scripts** — open any `scene.py` in Blender and tweak materials, lighting, or timing before re-rendering
- **Add audio** — pipe the output MP4 through any audio tool (ElevenLabs, Bark, Audacity)
- **Integrate assets** — import your own `.fbx` or `.obj` models into the generated scripts
- **Batch production** — wrap `run.py` in a loop to render a series of variations automatically

---

## Technical Architecture

```
blender_ai/
├── main.py            CLI entry point (click)
├── models.py          Data classes: Shot, MovieConfig, RunResult
├── prompts.py         System prompts for storyboard, scripting, vision
├── llm_client.py      Anthropic API wrapper (storyboard, script gen, vision, fix)
├── blender_runner.py  Blender subprocess management + error detection
├── video_assembler.py ffmpeg wrapper (frames → MP4)
└── orchestrator.py    Full pipeline: storyboard → render loop → assembly
```

The LLM layer (`llm_client.py`) is decoupled from the rendering layer — replacing Claude with another model requires changing only one file.

---

## Roadmap

- [ ] Audio track generation (voice-over + ambient sound)
- [ ] Import custom 3D assets (FBX, OBJ, GLB)
- [ ] Web UI for non-technical users
- [ ] Multi-GPU parallel shot rendering
- [ ] Export to Blender `.blend` file for manual refinement
- [ ] Support for character rigs and dialogue animation

---

## Philosophy

3D animation has always required years of specialist training. Blender AI does not replace that craft — it removes the barrier to entry for everyone else. A designer with a strong visual instinct but no Blender experience can now produce motion work. A filmmaker can pre-visualise a sequence in an afternoon. An educator can generate bespoke visualisations on demand.

The goal is not to automate creativity. It is to make the tools of 3D production accessible to anyone who has something to say.

---

## License

MIT — free to use, modify, and distribute.

---

*Built with [Blender](https://blender.org) · [Claude](https://anthropic.com) · [ffmpeg](https://ffmpeg.org)*
