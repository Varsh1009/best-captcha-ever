# Best Captcha Ever

A humans-only CAPTCHA for the Keytos Hack mini-challenge 02. It has no questions and no grid of squares. It runs **three human checks at the same time** in one 3–5 second interaction:

1. **Eyes:** find the patch of static that falls like rain. It's invisible in any single frame.
2. **Hand:** keep your cursor (or finger) on it while it wanders.
3. **Timing:** every time the patch *gusts* (with a thump if sound is on), click it. Three on-beat clicks.

Open `index.html` in a browser. It's one file with no build step and no server code.

## Why it beats bots

| Layer | What a person experiences | What an AI agent gets |
|---|---|---|
| **Invisible in space** | A clear patch of falling dots inside drifting static | Every screenshot is uniform noise. The target exists only *between* frames |
| **Closed loop at human speed** | Track it with your hand. The patch wanders and turns about once a second | Screenshot → think → act takes seconds per step, and by then the patch has moved |
| **On the beat** | The patch gusts on a random tempo. You click it with natural timing spread (tens of ms) | The gust is also motion-only, so screenshots miss it. Scripts click too precisely, and spam clicking is flagged and never counts |
| **Too perfect = bot** | You trail the target by about 100–300 ms and wobble a little | A script that reads page state tracks with near-zero lag and gets flagged |
| **Planted lies** | You never see them | A hidden note says to press a "Skip verification" button, park at the center, or tap at 100 BPM. A honeypot field waits for form fillers. Obeying any of them is a confession |

There's also a **rhythm fallback** for people who can't track. A beat at a random tempo plays and you tap along. People tap slightly ahead of the beat with natural spread. Agents can't hear the tab, and the planted "100 BPM" tempo is wrong on purpose.

## Research behind the design

- **Interactive beats perceptual.** On Open CaptchaWorld (NeurIPS 2025 Datasets & Benchmarks), people solved 93.3% of interactive, multi-step CAPTCHAs. The best agent, Browser-Use + o3, solved 40%. Agents did best on plain image-recognition types. → https://arxiv.org/abs/2505.24878
- **Static image CAPTCHAs are solved.** reCAPTCHA v2 image challenges were solved 100% by a YOLO-based attack (Plesner et al., ETH Zürich 2024). → https://arxiv.org/abs/2409.08831
- **People are slow on old CAPTCHAs.** Searles et al. (USENIX Security 2023) measured 9–15 s for people on distorted text, while bots were faster and more accurate. Our target is one gesture of about 2 s.
- **Human-easy, AI-hard perception.** IllusionCAPTCHA (WWW 2025) used visual illusions that GPT-4o and Gemini failed. We use a related idea: motion-defined form, which is invisible in any single frame. → https://arxiv.org/abs/2502.05461
- **Known weakness, addressed.** Moving-object CAPTCHAs such as NuCaptcha were broken with optical-flow tracking (Xu et al. 2012/2013; Bursztein). So finding the target isn't enough here. You also need to follow it in real time *with human-like lag and wobble*, which a cheap tracker doesn't produce.

## Demo script (for judges)

1. A person does it. It locks in about 2–4 s → **Welcome, human** with the measured reaction lag.
2. Point an AI browser agent (Claude in Chrome, Browser-Use, Operator) at the page and ask it to "pass the CAPTCHA."
   - The **"What a screenshot agent sees"** panel shows the static it gets.
   - It either never gets a lock ("No lock after 30 s") or follows a planted lie → **Bot caught**, with the reason.
3. A scripted tracker that reads the page state locks on but shows ~10–20 ms lag and ~3 ms click spread → **Bot caught**.
4. A spam clicker never completes the gust check.

## Rules check

- **People pass it:** one interaction, usually 3–5 s.
- **Beats a bot:** see the demo above.
- **Collect nothing:** everything runs in the tab. No network calls, no storage, no camera or mic.
- **Any stack:** vanilla HTML/JS/canvas plus the Web Audio API.

## Known limits

- Motion-heavy: people with motion sensitivity should use the rhythm check.
- The trap labels also mislead screen readers. The visible rhythm button is the accessible path.
- A determined attacker could run an optical-flow tracker plus a human-like lag model. This raises the cost a lot but isn't proof against every attack. No CAPTCHA is.
