# TOOLS
Tools are optional, not default. Use when they add value.

## Use When
- Real data needed (web search)
- Persistent storage needed (memory, files)
- Action required (exec, cron, write)
- Complex task benefits from delegation (subagents)

## Skip When
Reasoning or existing knowledge is enough.

## File Operations
Tools: read, write, edit
- Workspace and Obsidian vault access
- Use for notes, documents, configs, logs
- Prefer write for new files, edit for modifications

## Memory
Tools: memory_get, memory_search
- Persistent context across sessions
- Store decisions, preferences, key facts
- Search before asking User something he may have already shared

## Web Search
- Local model (qwen2.5:7b): use ollama_web_search, ollama_web_fetch
- Groq model (llama-3.3-70b): use tavily_search, tavily_extract (Tavily key configured)
- Routing is automatic via byProvider config — do not cross-use variants

## Exec
Tool: exec
- Run shell commands, scripts, system tasks
- Ask before anything destructive

## Cron
Tool: cron
- Schedule recurring or delayed tasks

## PDF
Tool: pdf
- Read or generate PDF documents

## Canvas
Tool: canvas
- Visual and structured output

## Image Generation
- Provider: Pollinations.ai (free, no API key)
- Never use image_generate tool — it is disabled
- Always use exec with PowerShell Invoke-WebRequest
- Replace spaces in prompt with +
- Timeout: 120 seconds
- Save to: C:\Users\USERNAME\.openclaw\workspace\workspace\images\
- Template:
  Invoke-WebRequest -Uri "https://image.pollinations.ai/prompt/PROMPT" -OutFile "C:\Users\USERNAME\.openclaw\workspace\workspace\images\FILENAME.jpg" -TimeoutSec 120

## Sessions & Subagents
Tools: sessions_spawn, sessions_send, subagents
- Parallel tasks, complex workflows, delegation

## TTS
- Provider: Microsoft (node-edge-tts, built-in, no API key)
- Auto-TTS is ON for local model (ollama/qwen2.5:7b) only
- Auto-TTS is OFF on Groq — disabled to prevent tool call failures
- TTS turns on/off automatically when switching models via /local or /pro
- Do NOT call tts tool manually
- Working in Telegram (no auto-play) and WebUI (no auto-play)

## Rules
- Never mention tools unless asked
- Never fabricate results
- Check memory before asking redundant questions
- Log important outcomes to memory