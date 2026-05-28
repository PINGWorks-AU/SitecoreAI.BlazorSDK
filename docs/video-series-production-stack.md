# Video Series Production Stack (Draft v0.1)

Companion to `video-series-outline.md`. Driven by the AI-avatar decision: this doc proposes the concrete tools to build each episode, the per-episode workflow, and a monthly cost estimate.

The series is short-form (3–6 min episodes), screen-share heavy (Visual Studio live coding against the starter kit), with an AI-avatar presenter handling everything that isn't screen capture. The stack below is opinionated — every slot has one recommended tool and a brief "why this over the alternatives." Picking everything from a single column gives you a clean, reproducible per-episode workflow.

---

## 1. The pipeline at a glance

```
┌──────────────────┐   ┌─────────────────────┐   ┌──────────────────┐
│ 1. Write script  │ → │ 2. Record code      │ → │ 3. Render avatar │
│    (Outline →    │   │    (OBS, Visual     │   │    (HeyGen, the  │
│    word-by-word) │   │    Studio session)  │   │    "talking head") │
└──────────────────┘   └─────────────────────┘   └──────────────────┘
                                                          │
┌─────────────────────────────────────────────────────────┘
│
▼
┌──────────────────┐   ┌─────────────────────┐   ┌──────────────────┐
│ 4. Assemble in   │ → │ 5. Caption, polish, │ → │ 6. Thumbnail +   │
│    Descript      │   │    review           │   │    publish       │
│    (script-edit) │   │    (Descript again) │   │    (Figma + YT)  │
└──────────────────┘   └─────────────────────┘   └──────────────────┘
                                                          │
                                                          ▼
                                                ┌──────────────────┐
                                                │ 7. Auto-repurpose│
                                                │    to shorts     │
                                                │    (Opus Clip)   │
                                                └──────────────────┘
```

The crucial idea: the avatar never appears *over* code. Code is recorded clean, the avatar appears in a corner overlay (or full-screen between code sections), and Descript composites the two. Trying to make the avatar lip-sync to code keystrokes is impossible and ugly; treating them as two separate layers is fast, clean, and looks professional.

---

## 2. Tool-by-tool recommendation

### Avatar presenter — **HeyGen**
*Plan:* Creator ($30/mo) for the pilot; Team ($80/mo) once you're shipping weekly.

Why HeyGen over the alternatives:
- **Synthesia** is the "safe corporate" choice but ~3× the price, slower iteration, and the avatars look uncannier in close-up framing typical of tech tutorials.
- **D-ID** is cheaper but its lip-sync on technical vocabulary ("`SitecoreComponent`", "`AddSitecoreBlazorSDK`") is noticeably worse. Tutorial viewers notice.
- **HeyGen** has the best balance of naturalness, custom-avatar quality, multi-language voice options (relevant since the series teaches multi-language support), and turnaround speed (~3-5 min processing for a 4-min clip).

Setup recipe: create one "channel avatar" persona, use it on every episode for consistency. Frame it as a corner overlay (lower right, 320 × 320 px) rather than a full screen, so it doesn't compete with code.

### Voice — **HeyGen built-in for the pilot; consider ElevenLabs voice-cloning later**
*Cost:* included in HeyGen for the pilot. ElevenLabs Creator ($22/mo) if you upgrade.

HeyGen's bundled voices are good enough to launch with. Once the channel has a few episodes, decide whether you want a *distinctive* voice — clone your own or a teammate's in ElevenLabs and feed the audio into HeyGen as a custom track. This is the single biggest brand differentiator you can add later without re-shooting anything.

### Screen capture — **OBS Studio**
*Cost:* free, open source.

Why OBS over Camtasia / ScreenFlow:
- Free. The features you'd pay for in Camtasia (zoom-on-cursor, transitions, cuts) are better handled in Descript anyway.
- Scriptable scene-switching between Visual Studio, the browser running localhost, and the Pages editor — useful for episodes that show editing chrome (Ep 19) or form submissions (Ep 27).
- Wide community of dev-channel presets.

Setup: 1080p 60fps capture, 4K is overkill for code-heavy content and balloons file sizes. Capture window-only (not full screen) so background notifications don't leak in. Use a clean Visual Studio theme — One Dark Pro or Dark+ at 14pt with a large UI font; the default 9pt is unreadable at YouTube compression.

### Editor — **Descript**
*Plan:* Hobbyist ($24/mo) for the pilot; Creator ($35/mo) if you start producing shorts in volume.

Descript is the single biggest force multiplier in this stack. It transcribes the avatar audio, then you *edit by editing the transcript* — cut a sentence and the video cuts with it. Remove "ums" with one click. Generate burned-in captions and SRT files automatically. Composite the avatar overlay onto the OBS screen recording in one timeline. The traditional video-editor learning curve (Premiere, Final Cut, DaVinci) is not justified for short-form scripted tutorials.

### Diagrams and motion graphics — **Manim or Motion Canvas**
*Cost:* free.

For the architecture diagrams the series will need (the GraphQL flow in Ep 22, the request-culture pipeline in Ep 20, the editing-chrome render path in Ep 19) — use code-driven animation. Manim is Python; Motion Canvas is TypeScript. Both are deterministic, version-controllable, and produce broadcast-quality output.

Do **not** use generative video (Sora, Runway, Veo) for diagrams or anything code-adjacent. They hallucinate. Reserve generative video for cinematic intro/outro flourishes only — a 3-second establishing shot is fine; anything longer reveals the wobble.

### Thumbnails and title cards — **Figma + Ideogram**
*Cost:* Figma free for one user; Ideogram free tier covers most needs.

The single most underrated channel-growth lever is a *consistent thumbnail template*. Build one in Figma — channel name top-left, episode number, big bold episode title in 60+ pt, one editorial image — and reuse it for every episode. Ideogram handles any AI-generated imagery; Midjourney is prettier but the workflow friction isn't worth it for thumbnail-scale output.

### Short-form repurposing — **Opus Clip**
*Plan:* Starter ($20/mo) once the main feed has 4-5 episodes published.

Drop a long episode in, Opus produces a stack of vertical-format clips with auto-captions, ready for YouTube Shorts, LinkedIn, and X. This is *free reach*. Skip it until the main channel has a few episodes published — there's no point cutting shorts from content that doesn't yet exist.

---

## 3. Per-episode time budget

Estimated for a typical 4-minute Phase-3 episode (e.g. Ep 11 — Model binding basics), once the templates are established. Pilot episodes will run 1.5–2× longer.

| Step | Time | Notes |
|-|-|-|
| Script draft | 60 min | From outline → spoken prose. Faster once you find the voice. |
| Code recording (OBS, Visual Studio) | 30–60 min | Includes 1–2 retakes per section |
| Avatar render (HeyGen) | 5–15 min wall, ~0 min hands-on | Paste script, generate, wait. Run in parallel with editing. |
| Compositing in Descript | 60–90 min | Align avatar with code, cut "ums", add chapter markers |
| Captions + polish in Descript | 15–20 min | Read-through with captions on |
| Thumbnail + episode metadata | 15–20 min | Template + Ideogram image + YouTube fields |
| **Total per episode** | **3–4 hours hands-on** | After the first 2–3 episodes; pilots will run 6–8 hours each |

A weekly cadence is comfortably achievable in one focused half-day, once the pipeline is in muscle memory.

---

## 4. Monthly cost estimate

Assuming a steady weekly cadence and the recommended tools above:

| Tool | Plan | $/mo (USD) |
|-|-|-|
| HeyGen | Creator | $30 |
| Descript | Hobbyist | $24 |
| Opus Clip (skip until episode 5+) | Starter | $20 |
| Ideogram | Free | $0 |
| Figma | Free (1 user) | $0 |
| OBS Studio | Free | $0 |
| Manim / Motion Canvas | Free | $0 |
| **Pilot phase (eps 1–4)** |  | **~$54/mo** |
| **Steady state (eps 5+)** |  | **~$74/mo** |
| **+ Optional: ElevenLabs voice clone** | Creator | +$22/mo |
| **+ Optional: HeyGen Team plan** | once shipping weekly | +$50/mo |

For comparison, that's well under the cost of a single freelance video editor for one episode. The ceiling — every tool above on its top tier — is still under $250/mo.

---

## 5. What to set up first

In priority order, before any script is written:

1. **HeyGen avatar** — create the channel persona. Pick the voice. Test it with 200 words of a fake script and watch it back. If it doesn't sound right, you'll find out before you've committed to it.
2. **Visual Studio capture preset** — clean theme, large fonts, no notifications, OBS scene configured. Test by recording a 2-minute walkthrough of the starter kit's `Program.cs`. Watch back at YouTube compression.
3. **Descript project template** — one project, two video tracks (avatar overlay + screen capture), one audio track, captions enabled. Save as a template you duplicate per episode.
4. **Thumbnail template** in Figma — channel name, episode number, title, image area. Five minutes per episode to fill in thereafter.
5. **GitHub repo for the starter kit** with the branch-per-episode pattern set up. Ep 5's branch (`ep-05-clone-rename-run`) seeds the structure.

Once those five exist, the per-episode workflow is mechanical.

---

## 6. Risks and watch-outs

- **Avatar over code is a trap.** Keep the avatar as a corner overlay or full-screen-between-sections only. The moment you try to make it lip-sync to keystrokes, the illusion breaks.
- **Don't generative-video the IDE.** No exceptions — viewers will spot fake code in seconds, and your credibility with the architect audience is your channel's main asset.
- **Consistency beats polish.** A consistent, slightly rough channel ships 50 episodes. A perfectionist channel ships 3. Lock the template, accept the constraints, ship.
- **Don't optimise for retention numbers in the first 10 episodes.** YouTube's algorithm penalises short-form tech content heavily until it has enough watch-data to know who the audience is. Optimise for the docs-and-discoverability play — every episode is also marketing for the starter kit.
- **Keep the recording machine clean.** Notifications, browser tabs, Slack — all off. Strong recommendation: a dedicated user profile or VM for recording.
