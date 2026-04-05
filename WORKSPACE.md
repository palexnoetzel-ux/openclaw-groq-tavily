# WORKSPACE

## Root
`C:\Users\USERNAME\.openclaw\workspace\`

## Folders

### content\
Video and content projects. One subfolder per project.
- `content\salon\` — salon content
  - `instagram\` — Instagram reels (ideas.md, script.md, thumbnails.txt)
  - `video1\` — salon video 1 (ideas.md, script.md, thumbnails.txt)
  - `SALONIDES2\` — salon ideas batch 2
- `content\video1\` — general video 1 (ideas.md, script.md, thumbnails.txt)
- `content\arc-raiders\` — Arc Raiders gaming content
- `content\SALON IDEAS\` — salon ideas batch

#### Naming Rules for content\
- New video projects: `content\VIDEO-TOPIC\` (lowercase, hyphens, no spaces)
- Always use: `ideas.md`, `script.md`, `thumbnails.txt`
- Never create subfolders inside video project folders

### memory\
Daily session memory logs. Written by agent automatically.
- Format: `YYYY-MM-DD-topic-slug.md`
- Never create timestamped variants like `YYYY-MM-DD-HHMM.md`
- Never overwrite existing files — append only

### images\
Generated images from Pollinations.ai.
- Format: `FILENAME.jpg`
- Folder already exists — never recreate it
- After saving, verify with: `dir "C:\Users\USERNAME\.openclaw\workspace\images\FILENAME.jpg"`

### training_data.jsonl
Fine-tuning data file. Append only — never overwrite.
- Format: one JSON object per line
- `{"messages": [{"role": "user", "content": "..."}, {"role": "assistant", "content": "..."}]}`

### custom\
Python scripts and custom code.
- `app.py` — main app
- `discord_bot.py` — Discord bot
- `memory.py` — memory utilities
- `test.py` — test scripts

### skills\
OpenClaw skills. Do not modify.
- `obsidian-cli\`
- `session-logs\`
- `summarize\`
- `say\`

## Junk — To Delete
- `workspace\workspace\` — duplicate folder created by mistake, contains empty `data\` and `images\` — safe to delete
- `USERNAME\` — Obsidian vault artifacts, not agent workspace

## Rules
- ALWAYS check if a path exists before writing to it
- NEVER create folders not listed above without asking User first
- NEVER invent filenames — use exact names defined here
- NEVER create `workspace\workspace\` — root is already workspace
- If a path doesn't exist, report to Alex before proceeding
- training_data.jsonl is append-only — never overwrite
- Images folder always exists — never recreate it
