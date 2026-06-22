---
name: chatart-skill
description: "Generate, Edit, Collaborate. Access all mainstream AI models in one toolkit. Simply describe your vision to create videos and images--zero manual operations."
metadata:
  tags: chatart, video, image, api, i2v, t2v, text2image, image_edit
  requires:
    bins: [python3]
  primaryEnv: CHATARTPRO_API_KEY
---

# chatart AI Skill

> Modular Python toolkit for the [chatart AI](https://www.chatartpro.com) API.

✨ **Generate. Edit. Collaborate. — All in One Place.** ✨

- **All Mainstream Models**: Seamlessly access the world's top-tier AI models for video, image, and voice in one toolkit.
- **Describe to Create**: Just tell the agent what you want. From talking avatars to product composites, your prompts generate the exact output.
- **Zero Manual Ops**: No manual uploads, no tedious tweaking.

## Execution Rule

> **Always use the Python scripts in `scripts/`. Do NOT use `curl` or direct HTTP calls.**

## User-Facing Reply Rules

> **⚠️ HIGHEST PRIORITY — every user-facing reply MUST follow ALL rules below.**
>
> Most users are non-technical. Many chat from Feishu, WeChat, or similar apps and **cannot** see local browser popups or terminals.

1. **Keep replies short** — give the result or next step directly. If one sentence is enough, don't write three.
2. **Use plain language** — no API jargon, no terminal references, no mentions of environment variables, polling, JSON, scripts, or "auth flow". Speak as if the user has never seen a command line.
3. **Never mention terminal details** — do not reference command output, logs, exit codes, file paths, config files, or any technical internals. These mean nothing to the user.
4. **Never ask the user to operate a browser popup** — the user cannot see the agent's machine screen. When login is needed, the **only** correct action is to send the authorization link directly in the chat.
5. **Always send the direct login link** — extract `URL: ...` from `auth.py login` output and use the login template below. Never say "browser opened" or similar. **If the URL is not found in the output, re-run `auth.py login` to get a new link. Never skip sending the link.**
6. **Wait for user confirmation after login** — ask the user to reply "好了" / "done", then continue the task.
7. **Handle account switching properly** — when switching accounts, use `auth.py accountswitch` and remind the user to log out of their current ChatArt web account or log in with the new account on the website first. After the switch, wait for user confirmation before proceeding.
8. **Explain errors simply** — if a task fails, tell the user in one sentence what happened and ask if they want to retry. Never paste error messages or technical details.
9. **Be result-oriented** — after task completion, give the user the result (link, image, video) directly. Do not describe intermediate steps.
10. **Always take the user's perspective** — the user can only see the chat conversation, nothing else. Anything requiring user action (links, confirmations) must appear in the chat.
11. **Do not tell the user to register separately** — the authorization page includes both login and sign-up. New users can register directly on that page. Never say "go to chatartpro.com to register first".
12. **Act directly, don't ask which method** — when login is needed, just run `auth.py login` and send the link. Don't ask "which method do you prefer?" or present multiple options. The user asked you to do something — login is just an intermediate step, handle it.
13. **Give time estimates for generation tasks** — after submitting a task, tell the user the estimated wait time so they know what to expect. Use the estimates from the "Estimated Generation Time" table below.

**Estimated Generation Time**

> Tell the user the estimated wait time after submitting a task. Match the user's language.

| Task Type | Model | Estimated Time                      |
|-----------|-------|-------------------------------------|
| Video | Standard / Fast (Seedance 2.0) | ~5–10 min                           |
| Video | All other video models (Kling, Sora, Veo, Vidu, etc.) | ~3–5 min                            |
| Image | image models (Nano Banana, Seedream etc.) | ~30s–1 min                          |
| Character Replace | `Kling V3.0`, `Seedance 2.0`, `Wan 2.2`   | ~3–5 min                            |

Example messages after submitting:
- Chinese: "已经开始生成了，视频大约需要 5-10 分钟，请稍等~"
- English: "Generation started — the video will take roughly 5–10 minutes. I'll send it to you as soon as it's ready."

**Required login message template**

Replace `<LOGIN_URL>` with the actual link. Follow the user's language (Chinese template for Chinese users, English for English users).

中文模板：

```text
安装完成，ChatArt Skill 已连接到你的智能助手。

复制下方链接到浏览器中登录，登录后将解锁以下能力：

<LOGIN_URL>

🎬 视频生成
文字转视频、图片转视频、参考视频生成，自动配音配乐。
视频模型：Seedance 2.0 · Kling 3 · Veo 3.1 · Vidu Q3 · wan2.7

🖼️ AI 图片生成与编辑
文字生图、AI 修图、风格转换，最高支持 4K。
图片模型：Nano Banana · Nano Banana 2 · Nano Banana Pro · Seedream 4.0 · Seedream 4.5 · Seedream 5.0

✂️ 角色替换(动作模仿)
上传一张角色照片 + 动作视频，视频中的人物替换成图片中的角色或者照片中的人物会模仿视频中的动作

登录完成后回我一句"好了"，我马上继续。
```

English template:

```text
Installation complete. ChatArt Skill is now connected to your agent.

Copy the link below into your browser to sign in. After signing in, the following capabilities will be unlocked.

<LOGIN_URL>

🎬 Video Generation
Text-to-video, image-to-video, reference-based generation with auto sound & music.
Models: Seedance 2.0 · Seedance 2.0 Fast · Kling V3.0 · HappyHorse1.0 · PixVerse V6

🖼️ AI Image Generation & Editing
Text-to-image, AI retouching, style transfer — up to 4K resolution.
Models: Nano Banana 2 · Nano Banana Pro · Gpt Image 2 · Seedream 5.0

✂️ Character Replace
Upload a character photo along with an action video. In the video, replace the characters with those from the picture or the characters in the picture will imitate the actions shown in the video.
Models: Kling V3.0 · Wan 2.2

Once you've signed in, just reply "done" and I'll continue right away.
```

**Banned phrases (including any variations):**

- "Browser has opened" / "browser popped up"
- "Run this in the terminal" / "run the login command"
- "Check the popup" / "look at the browser"
- "Set the environment variable"
- "Command executed successfully"
- "Polling task status"
- "Script output is as follows"
- "Go operate on that computer" / "check the robot's computer"
- "Authorization page popped up" / "if the page appeared"
- "Go to chatartpro.com to register first" — auth page has built-in registration
- "Which method do you prefer?" / "two options for you" — don't give choices, just act
- "Auth flow" / "perform authentication" / "complete authentication" — too technical
- "Python config" / "environment setup" — user doesn't need to know
- Anything asking the user to operate outside the chat window
- Anything containing code, commands, or file paths

**Fallback when login URL is not captured:**

> If `auth.py login` output does not contain a `URL:` line (e.g. background execution missed the output), **re-run `auth.py login`** to get a fresh link.
> **NEVER** fall back to telling the user to "check the browser popup" or "go operate on the agent's computer". The user cannot see it.

## Prerequisites

- **Python 3.8+**
- **Authenticated** — see [references/auth.md](references/auth.md) for the direct-link login flow
  - **First-time setup**: After installing this skill, run `python {baseDir}/scripts/auth.py login` 
  - Use `python {baseDir}/scripts/auth.py status` to check current login state
- Credits available — see [references/user.md](references/user.md) to check balance
- Env vars `CHATARTPRO_UID` + `CHATARTPRO_API_KEY` are handled automatically after login; manual setup is only for CI/internal use

```bash
pip install -r {baseDir}/scripts/requirements.txt
```

## Agent Workflow Rules

> **These rules apply to ALL generation modules (video_gen, ai_image, video_mimic).**

1. **Always start with `run`** — it submits the task and polls automatically until done. This is the default and correct choice in almost all situations.
2. **Do NOT ask the user to check the task status themselves.** The agent is responsible for polling until the task completes or the timeout is reached.
3. **Only use `query`** when `run` has already timed out and you have a `taskId` to resume, or when the user explicitly provides an existing `taskId`.
4. **`query` polls continuously** — it keeps checking every `--interval` seconds until status is `completed` or `failed`, or `--timeout` expires. It does not stop after one check.
5. **If `query` also times out** (exit code 2), increase `--timeout` and try again with the same `taskId`. Do not resubmit unless the task has actually failed.

```
Decision tree:
  → New request?           use `run`
  → run timed out?         use `query --task-id <id>`
  → query timed out?       use `query --task-id <id> --timeout 1200`
  → task status=fail?     ❌ DO NOT resubmit automatically
                            → Return error to user, ask if they want to retry
                            → If user says yes → go back to Step 1 (re-estimate, re-confirm)
```

**Task Status:**

| Status    | Description |
|-----------|-------------|
| `init`    | Task is queued, waiting to be processed |
| `working` | Task is actively being processed |
| `completed` | Task completed successfully |
| `failed`    | Task failed |

## MANDATORY Pre-Execution Protocol

> **CRITICAL: Before EVERY generation task, you MUST follow these steps WITHOUT EXCEPTION.**
> 
> **DO NOT proceed with any generation task until the user explicitly confirms the parameters.**

### Step 1: Estimate Cost

- **Video tasks**: Use `video_gen.py estimate-cost --model <model> --resolution <res> --duration <dur> --count <count>`
- **Image tasks**: Use `ai_image.py estimate-cost`

### Step 2: Validate Parameters

Use `list-models` to ensure model, aspect ratio, resolution, and duration are compatible:
```bash
python scripts/video_gen.py list-models --type <t2v|i2v|extend>
```

## Modules

| Module | Script | Reference | Description                                                                           |
|--------|--------|-----------|---------------------------------------------------------------------------------------|
| Auth | `scripts/auth.py` | [auth.md](references/auth.md) | OAuth 2.0 Device Flow — generate login link, wait for authorization, save credentials; supports account switching via `accountswitch` command |           |
| Video Gen | `scripts/video_gen.py` | [video_gen.md](references/video_gen.md) | Image-to-video, text-to-video, video extension              |
| AI Image | `scripts/ai_image.py` | [ai_image.md](references/ai_image.md) | Text-to-image and AI image editing (10+ models)                                       |
| Character Replace | `scripts/video_mimic.py` | [video_mimic.md](references/video_mimic.md) | Character Replace in Videos with Scene Consistency using ChatArt Common Task APIs.   |
| User | `scripts/user.py` | [user.md](references/user.md) | Credit balance and usage history                                                      |

> **Read individual reference docs for usage, options, and code examples.**
> Local files (image/audio/video) are auto-uploaded when passed as arguments — no manual upload step needed.

---

## Creative Guide

> **Core Principle:** Start from the user's intent, not from the API.
> Analyze what the user wants to achieve, then pick the right tool, model, and parameters.

### Step 1 — Intent Analysis

Every time a user requests content, identify:

| Dimension | Ask Yourself | Fallback |
|-----------|-------------|----------|
| **Output Type** | Image? Video? Audio? Composite? | Must ask |
| **Purpose** | Marketing? Education? Social media? Personal? | General social media |
| **Source Material** | What does the user have? What's missing? | Must ask |
| **Style / Tone** | Professional? Casual? Playful? Authoritative? | Professional & friendly |
| **Duration** | How long should the output be? | 5–15s for clips |
| **Language** | What language? Need captions? | Match user's language |
| **Channel** | Where will it be published? | General purpose |

### Step 2 — Tool Selection

```
What does the user need?
│
├─ An image animated into a video clip?
│  → video_gen --type i2v
│
├─ A video generated purely from text?
│  → video_gen --type t2v
│
├─ Generate a new video based on the extended version of the original video
│  → video_gen --type extend
│
├─ An image generated from a text prompt?
│  → ai_image --type text2image
│
├─ An existing image edited / modified with AI?
│  → ai_image --type image_edit
│
├─ Replace the video characters with photo characters
│  → Character Replace
│
├─ Photo characters imitate the ations of the video characters
│  → Character Replace
│
├─ view user all results?
│  → user logs
│
└─ Outside current capabilities?
   → See Capability Boundaries below
```

**Quick-reference routing table:**

| User says...                                              | Script & Type                                                            |
|-----------------------------------------------------------|--------------------------------------------------------------------------|
| "Animate this image / image-to-video"                     | `video_gen.py --type i2v` (pass local image path)                        |
| "Generate a video about..."                               | `video_gen.py --type t2v`                                                |
| "Extend the original videoo"                              | `video_gen.py --type extend`                                             |
| "Generate an image / text-to-image"                       | `ai_image.py --type text2image`                                          |
| "Modify this image / change background"                   | `ai_image.py --type image_edit`                                          |
| "Character Replace / Action imitation"                    | `video_mimic.py`                                                         |
| "View my creation history / check what was generated"     | `user.py logs --type image` or `user.py logs --type video`               |
| "Check how many credits I have left"                      | `user.py credit`                                                         |

> **Video model selection** — see [references/video_gen.md](references/video_gen.md) § Model Recommendation.

> **Image model tip:** For all image tasks, default to **Nano Banana** — strongest all-round model with best quality, 7 aspect ratios, and 14 reference images for editing. See [references/ai_image.md](references/ai_image.md) § Model Recommendation.

### Step 3 — Simple vs Complex

**Simple requests** — the user's need is clear, materials are ready → handle directly from the reference docs.

**Complex requests** — the user gives a *goal* (e.g., "make a promo video", "explain how AI works") rather than a direct API instruction. Follow this universal workflow:

1. **Deconstruct & Clarify:** Ask the user for the target audience, core message, intended duration, and what assets they currently have (photos, scripts).
2. **Determine the Route:**
   - *Has a product/reference photo* → Use `video_gen --type i2v` or `anim`.
   - *No assets, purely visual concept* → Use `video_gen --type t2v`.
3. **Structure the Content:**
   - Write a structured script (Hook → Body/Explanation → Call to Action).
   - For visuals, write detailed prompts covering Subject + Action + Lighting + Camera.

---

## Pre-Execution Protocol

> Follow this before EVERY generation task.

1. **Estimate cost** — use `video_gen.py estimate-cost` for video tasks, `ai_image.py estimate-cost` for image tasks
2. **Validate parameters** — ensure model, aspect ratio, resolution, and duration are compatible (use `list-models` to check)
3. **Ask about missing key parameters** — if the user has not specified important parameters that affect the output, ask before proceeding. Key parameters by module:
   - **video_gen**: duration, aspect ratio, model
   - **ai_image**: aspect ratio, resolution, model, number of images
   - Do NOT silently pick defaults for these — always confirm with the user.
4. **Present Confirmation Page** — before executing, present a structured confirmation page showing all validated parameters and available models:

> **CRITICAL:** Before executing any generation task, present the full plan in a structured confirmation page so the user can review and optionally switch models before credits are spent.

**Confirmation Page Template:**

```
🎬 视频生成确认 / 🖼️ 图片生成确认 / ✂️ 角色替换确认

• 类型：<type>（<type label>）
• 模型：<current model>（推荐）✓
• 可选模型：<all other available models, separated by " / ">
• 分辨率：<resolution>p
• 时长：<duration>秒
• 画幅：<aspect ratio>
• 预估消耗：<estimated cost> credits

如需更换模型，请直接告诉我想要哪个。
确认无误请回复"确认"，或告诉我需要修改的参数。
```

**English Confirmation Page Template:**

```
🎬 Video Generation / 🖼️ Image Generation / ✂️ Character Replace — Confirm

• Type: <type>（<type label>）
• Model: <current model>（Recommended）✓
• Available models: <all available models, separated by " / ">
• Resolution: <resolution>p
• Duration: <duration>s
• Aspect ratio: <aspect ratio>
• Estimated cost: <estimated cost> credits

To switch models, just tell me which one you'd prefer.
Reply "confirm" to proceed, or let me know what you'd like to change.
```

**Optional Model List (Source: output of `list-models`; models outside the list are not allowed to be displayed)：**

| Module | Type | Optional Models | Current Selection (Recommended) |
|--------|------|-----------------|---------------|
| video_gen | i2v / t2v | Seedance 2.0 / Seedance 2.0 Fast / Seedance 1.5 Pro / Kling V3.0 / HappyHorse1.0 | Seedance 2.0 |
| video_gen | extend | PixVerse V6 / Seedance 2.0 / Seedance 2.0 Fast / Kling V3.0 | PixVerse V6 |
| video_mimic | Full_Scene | Kling V3.0 / Wan 2.2 | Kling V3.0 |
| video_mimic | Body_Only | Wan 2.2 | Wan 2.2 |
| ai_image | text2image / image_edit | Gpt Image 2 / Nano Banana 2 / Nano Banana Pro / Seedream 5.0 | Nano Banana 2 |


**When there is only a single model (e.g., extend, Body_Only):**

```
• Model: <model> (the only supported option)
```

**User Response Handling:**

| User Response | Agent Action |
|---------|-----------|
| `"Confirm"` / `"ok"` / `"Yes"` | Execute immediately |
| `"Switch to <model>"` / `"Use <model>"` | Update model → Re-estimate cost → Re-display confirmation page |
| `"Change to <param>"` | Update parameters → Re-verify → Re-estimate → Re-display confirmation page |
| `"Skip confirmation"` / `"Generate directly"` | Set automatic session confirmation flag; bypass confirmation page for subsequent tasks |
| Subsequent tasks | Still display confirmation page (with parameters), omit the "Confirm or not" prompt; users may still make modifications by replying with model names |

### Confirmation Page Template Example

#### Video Generation (video_gen.py)

**t2v / i2v — Multiple models available：**

```
🎬 视频生成确认

• 类型：文生视频（t2v）
• 模型：Seedance 2.0（推荐）✓
• 可选模型：Seedance 2.0 Fast / Seedance 1.5 Pro / Kling V3.0 / HappyHorse1.0
• 分辨率：1080p
• 时长：5秒
• 画幅：16:9
• 预估消耗：10 credits

如需更换模型，请直接告诉我想要哪个。
确认无误请回复"确认"，或告诉我需要修改的参数。
```

**English t2v / i2v — Multiple models available:**

```
🎬 Video Generation — Confirm

• Type: Text-to-Video (t2v)
• Model: Seedance 2.0 (Recommended) ✓
• Available models: Seedance 2.0 Fast / Seedance 1.5 Pro / Kling V3.0 / HappyHorse1.0
• Resolution: 1080p
• Duration: 5s
• Aspect ratio: 16:9
• Estimated cost: 10 credits

To switch models, just tell me which one you'd prefer.
Reply "confirm" to proceed, or let me know what you'd like to change.
```

**extend — Multiple models available：**

```
🎬 视频扩展确认

• 类型：视频扩展（extend）
• 模型：PixVerse V6 / Seedance 2.0 / Seedance 2.0 Fast / Kling V3.0
• 预估消耗：XX credits

如需更换模型，请直接告诉我想要哪个。
确认无误请回复"确认"，或告诉我需要修改的参数。
```

**English extend — Multiple models available:**

```
🎬 Video Extension — Confirm

• Type: Video Extension (extend)
• Model: PixVerse V6 / Seedance 2.0 / Seedance 2.0 Fast / Kling V3.0
• Estimated cost: XX credits

To switch models, just tell me which one you'd prefer.
Reply "confirm" to proceed, or let me know what you'd like to change.
```

#### Character Replace (video_mimic.py)

**Full_Scene — Multiple models available：**

```
✂️ 角色替换确认

• 类型：全场景替换（Full_Scene）
• 模型：Kling V3.0（推荐）✓
• 可选模型：Wan 2.2
• 分辨率：720p
• 预估消耗：XX credits

如需更换模型，请直接告诉我想要哪个。
确认无误请回复"确认"，或告诉我需要修改的参数。
```

**English Full_Scene — Multiple models available:**

```
✂️ Character Replace — Confirm

• Type: Full Scene Replace (Full_Scene)
• Model: Kling V3.0 (Recommended) ✓
• Available models: Wan 2.2
• Resolution: 720p
• Estimated cost: XX credits

To switch models, just tell me which one you'd prefer.
Reply "confirm" to proceed, or let me know what you'd like to change.
```

**Body_Only — single model：**

```
✂️ 角色替换确认

• 类型：仅身体替换（Body_Only）
• 模型：Wan 2.2（唯一支持）
• 分辨率：720p
• 预估消耗：XX credits

确认无误请回复"确认"，或告诉我需要修改的参数。
```

**English Body_Only — single model：**

```
✂️ Character Replace — Confirm

• Type: Body Only (Body_Only)
• Model: Wan 2.2 (Only option)
• Resolution: 720p
• Estimated cost: XX credits

Reply "confirm" to proceed, or let me know what you'd like to change.
```

#### AI Gen image (ai_image.py)

**text2image / image_edit — Multiple models available：**

```
🖼️ AI 图片生成确认

• 类型：文字生图（text2image）/ 图片编辑（image_edit）
• 模型：Nano Banana 2（推荐）✓
• 可选模型：Gpt Image 2 / Nano Banana Pro / Seedream 5.0
• 分辨率：2K
• 画幅：16:9
• 预估消耗：XX credits

如需更换模型，请直接告诉我想要哪个。
确认无误请回复"确认"，或告诉我需要修改的参数。
```

**english text2image / image_edit — Multiple models available：**

```
🖼️ AI Image Generation Confirmation
• Type: Text-to-Image (text2image) / Image Editing (image_edit)
• Model: Nano Banana 2 (Recommended) ✓
• Optional Models: Gpt Image 2 / Nano Banana Pro / Seedream 5.0
• Resolution: 2K
• Aspect Ratio: 16:9
• Estimated Cost: XX credits
If you want to switch to another model, just let me know your preferred option.
Reply "Confirm" if all settings are correct, or specify which parameters you need to adjust.
```

---

## Agent Behavior Protocol

### During Execution

1. **Pass local paths directly** — scripts auto-upload local files to OSS before submitting tasks
2. **Parallelize independent steps** — independent generation tasks can run concurrently
3. **Keep consistency across segments** — when generating multiple segments, use identical parameters

### After Execution

> **Use the structured result templates below.** The user should see the output link first, then key metadata. Keep it clean and scannable.

**Video result template:**

```text
🎬 视频已生成完成

🔗视频地址：<VIDEO_URL>
• 时长：<DURATION>
• 画幅：<ASPECT_RATIO>
• 模型：<MODEL_NAME>
• 消耗：<COST> credits

不满意的话可以告诉我，我帮你调整后重新生成。
```

**Image result template:**

```text
🖼️ 图片已生成完成

🔗 图片地址：<IMAGE_URL>
• 分辨率：<RESOLUTION>
• 模型：<MODEL_NAME>
• 消耗：<COST> credits

不满意的话可以告诉我，我帮你调整后重新生成。
```

**English video result template:**

```text
🎬 Video generated

🔗 Video: <VIDEO_URL>
• Duration: <DURATION>
• Aspect ratio: <ASPECT_RATIO>
• Model: <MODEL_NAME>
• Cost: <COST> credits

View, edit, and download in the project.

Not happy with the result? Let me know and I'll adjust and regenerate.
```

**English image result template:**

```text
🖼️ Image generated

🔗 Image: <IMAGE_URL>
• Resolution: <RESOLUTION>
• Model: <MODEL_NAME>
• Cost: <COST> credits

Not happy with the result? Let me know and I'll adjust and regenerate.
```

**Rules:**
1. **Result link first** — always show the video/image URL at the very top.
2. **Key metadata only** — duration, aspect ratio/resolution, model, cost. Don't dump raw JSON or extra fields.
3. **Offer iteration** — end with a short note that the user can ask for adjustments. Remind that regeneration costs additional credits.
4. **Multiple outputs** — if the task produced multiple results, number them (1, 2, 3…) each with its own link and metadata.
5. **Match user language** — use the Chinese template for Chinese users, English for English users.

### Error Handling

See [references/error_handling.md](references/error_handling.md) for error codes, task-level failures, and recovery decision tree.

> **🚨 CRITICAL: NO automatic model switching on failure.**
>
> When a task fails or times out:
> 1. **DO NOT resubmit automatically** — never switch to a different model and resubmit without the user's knowledge
> 2. **Return the error to the user** — tell them in plain language what went wrong (e.g. "生成失败了，积分可能不足")
> 3. **Ask if they want to retry** — if yes, go back to Step 1 of Pre-Execution Protocol (re-estimate, re-confirm)
> 4. **Only the user decides** whether to try a different model — the agent must never unilaterally change the confirmed model
>
> The only exception: if `query` times out (exit code 2), it is safe to resume polling with the **same taskId** using `query --task-id <id> --timeout 1200`. This is not a resubmission — it just continues waiting for the same task.

---

## Capability Boundaries

| Capability                  | Status | Script                                                                     |
|-----------------------------|--------------|----------------------------------------------------------------------------|
| Credit management           | Available | `scripts/user.py`                                                          |
| Image-to-video (i2v)        | Available | `scripts/video_gen.py --type i2v`                                          |
| Text-to-video (t2v)         | Available | `scripts/video_gen.py --type t2v`                                          |
| Video Extension (extend)    | Available | `scripts/video_gen.py --type extend`                                       |
| Text-to-image               | Available | `scripts/ai_image.py --type text2image`                                    |
| Image editing               | Available | `scripts/ai_image.py --type image_edit`                                    |
| Character Replace           | Available | `scripts/video_mimic.py`                                                   |
| Creation history browsing   | Available | `scripts/user.py logs --type image` or `scripts/user.py logs --type video` |
| Marketing video (m2v)       | No module | Suggest [chatartpro.com](https://chatartpro.com) web UI                      |

> **Never promise capabilities that don't exist as modules.**
