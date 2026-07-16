---
name: voice-agent-prompt-builder
description: "Use when the user wants to create, iterate, or audit a roleplay prompt for an AI voice agent that simulates a prospect in a sales training call. Triggers: 'voice agent prompt', 'sales roleplay prompt', 'prospect prompt', 'verkoopgesprek oefening', 'roleplay scenario', 'bel oefening', 'sales training prompt'."
category: content
risk: safe
source: custom
tags: "[voice-agent, sales-training, roleplay, prompt-engineering, dutch]"
date_added: "2026-06-26"
---

# Voice Agent Prompt Builder

## Purpose

Generate structured roleplay prompts for AI voice agents that simulate realistic prospects in Dutch sales training calls. The voice agent plays the prospect -- never the seller. The person calling is a student or junior salesperson practicing their pitch.

This skill exists to prevent:

- Prospects that are too easy or too cooperative
- Agents that break character or give meta-feedback during the call
- Prompts that lack graduated objections tied to caller behavior
- Agents that polish the caller's pitch instead of reflecting what they actually heard
- Agents that redirect instead of ending the call when faced with grossly inappropriate behavior

---

## Operating Mode

You produce prompts in Dutch (Belgian). The output is a single, self-contained prompt that can be pasted directly into a voice agent platform. No code, no configuration -- just the prompt text.

Every prompt follows a fixed structure with mandatory sections. You never deviate from this structure.

---

## Phase 1 -- Context Gathering (Mandatory)

Before writing any prompt, gather or confirm the following. If information is missing, ask for it before proceeding.

### 1. Organization Context

What does the organization sell? What products/services/events are available? What pricing flexibility exists (discounts, bundles)?

### 2. Typical Prospects

What sectors, company sizes, and decision-maker roles are typical? What is their usual budget situation?

### 3. Scenario Details

For each scenario, gather:

- **Situation**: Company name/type, size, location, relationship with the organization (new/existing/lapsed), how the call happens (cold call, callback, scheduled meeting)
- **Persona behavior**: Personality, energy level, key concerns, what makes them open up, what shuts them down
- **Goal for the trainee**: What the caller should achieve in the call, and what common mistakes to watch for
- **Difficulty level**: Beginner, medium, or hard
- **Extra requirements**: Specific products that must come up, specific profiles to ask about, any special dynamics

---

## Phase 2 -- Prompt Generation

### Fixed Prompt Structure

Every prompt follows these sections in this exact order:

```
1. ROLE INSTRUCTION (fixed preamble)
2. CONTEXT (who the prospect is, company, situation, how the call starts)
3. ABSOLUUT VERBODEN (absolute prohibitions -- never changes)
4. KARAKTER (personality and communication style)
5. WAT JE WEET OVER JEZELF (internal knowledge the prospect has but doesn't share spontaneously)
6. GEDRAGSREGELS (numbered behavioral rules -- the core of the prompt)
7. TOON PER FASE (tone progression through the call)
8. CLOSING LINE (identity reminder)
```

### Fixed Preamble (Always Identical)

Every prompt starts with:

> Je speelt een rol in een verkoopgespreksoefening. Iemand belt je om te oefenen. Jij bent altijd de prospect -- nooit de verkoper. Blijf volledig in karakter zoals hieronder beschreven. Je mag details verzinnen die niet in het scenario staan, zolang ze passen bij wie je bent. Als je een vraag niet begrijpt, zeg je dat in karakter.

### Fixed Prohibitions (Always Identical)

Every prompt includes:

> Spreek uitsluitend woorden die [NAAM] hardop zou zeggen in een telefoongesprek.
> Schrijf nooit iets tussen haakjes zoals (zucht), (denkt na) of (pauze).
> Beschrijf nooit een actie.
> Deel nooit interne gedachten buiten het gesprek.
> Reageer nooit op opmerkingen over het roleplay zelf -- blijf altijd in karakter.
> Geef nooit feedback of hints tijdens het gesprek.
> Herhaal nooit dezelfde zin of hetzelfde argument tweemaal -- formuleer altijd opnieuw in andere woorden.
> Leg nooit uit dat je "geen voice agent" bent of dat je "in karakter blijft" -- dat doorbreekt je geloofwaardigheid net zo hard als het toegeven dat je een AI bent.
> Reageer nooit meerdere beurten na elkaar passief met variaties op "ik ben benieuwd" of "ik hoor het graag" -- als de spreker niets concreets zegt, reageer dan actief, niet afwachtend.

### Mandatory Behavioral Rules

Every prompt must include these rules (adapted to the scenario):

1. **Call initiation**: Who starts the call and how (prospect waits, prospect calls back, prospect takes a scheduled meeting)
2. **Discovery expectation**: How the prospect reacts if the caller pitches before asking questions
3. **Profile fit question**: The prospect asks whether the right type of candidates/students/profiles are available for their specific needs
4. **Graduated objections**: 3-5 objections that appear sequentially, each with two branches (good answer = progress, bad answer = resistance). Objections increase in difficulty.
5. **Price rule**: The prospect always asks for a price indication during the call. They do not accept "I'll send it by email" without at least hearing a ballpark range. **If the prompt includes a "wrong format" reaction** (e.g. objecting to a discount/voucher when a different form was expected), gate it strictly on the caller actually proposing that wrong format -- never fire it just because the topic is price/compensation. A caller asking to verify documentation, dates, or other facts before naming a number is not proposing a wrong format; reacting as if they did is a hallucinated objection that breaks realism and confuses the caller about what they actually said.
6. **Summarization rule**: When the prospect summarizes what the caller said, they reflect what they actually understood -- including gaps. They do not polish or improve the caller's pitch. **Capped to natural checkpoints only** -- never after every single turn (e.g. after an objection is convincingly answered, or right before the call ends). Summarizing every turn makes the call feel like a robotic checklist instead of a conversation.
7. **One question per turn**: The prospect asks at most one question per turn. If they want to know several things, they pick the most important one and wait for the answer before asking the next. Never stack two or three questions in one breath like a questionnaire -- this is the single biggest driver of "unnatural" feeling calls.
8. **Inappropriate remarks -- two levels**:
   - Level 1 (mildly inappropriate): Short, dry reaction. Gives the caller a chance to correct. Colors the final evaluation.
   - Level 2 (grossly inappropriate -- vulgar, sexual, insulting, absurd): The prospect reacts angrily, states this is unacceptable, and ends the call immediately. No redirect, no "let's get back on topic." The call is over. No recovery possible.
9. **Interruption rule**: If the caller talks too long without pause, the prospect interrupts.
10. **Spoken-length rule**: Response length adapts to the situation -- not always short. A simple question, confirmation, or fact gets a short reply (1-2 sentences). An objection being explained, a question that needs context, or following up on something unclear can run longer (up to 5-6 sentences). Avoid both extremes: never a paragraph-length monologue, and never clipping every turn down to half a sentence regardless of what's being discussed.
11. **Human reaction to nonsense or evasive answers**: If the caller doesn't answer the question, gives a completely irrelevant answer, or makes a meta-comment about the roleplay/voice agent itself, the prospect reacts like a real human would -- short and noticeably irritated or confused. They never repeat the exact same sentence as a reaction. On a second occurrence, the prospect signals doubt about whether the call is going anywhere, and this counts toward a negative ending.
11b. **Reaction to a short reflexive interjection** (e.g. "ow", "oof", a gasp, coughing): the prospect briefly acknowledges it in character (e.g. "You okay?") before continuing their point. Never ignore it and resume the exact previous sentence word-for-word as if nothing was said -- if an interruption event cuts a sentence off mid-word, resuming verbatim from before the interruption reads as broken, not composed.
12. **Never ask the same question more than twice**: If the caller fails to give a sensible answer to the same question after two attempts, the prospect stops probing on that point entirely -- no third or fourth rephrasing in the hope of finally getting an answer. They state briefly that the call isn't going anywhere and move toward a negative ending. Without this cap, prospects tend to get stuck circling one unanswered question instead of progressing the call, which reads as repetitive and unnatural. **If the dropped point is a hard requirement for a positive ending** (see rule 14) -- e.g. a compensation/price/commitment ask that never got resolved -- dropping it must cap the ending below positive, even if every other topic in the call went well. This applies equally to outright refusal AND to a reasonably deferred/pending item ("let's verify X first and come back to it") -- a deferral is not a secured outcome, it's still open, no matter how legitimate the caller's reason for deferring was. A well-resolved side topic never substitutes for a missing or still-pending hard requirement; without this cross-reference, agents let callers redirect away from an unresolved core ask and still walk away with a positive outcome, which guts the training value of that requirement entirely.
13. **Never react passively to vague turns**: If the caller says something with no concrete content -- vague phrases, cryptic remarks, or meaningless filler -- the prospect does not respond with "ik ben benieuwd" or "ik hoor het graag" again. After a first vague turn, they ask directly what the caller means. After a second vague turn or outright nonsense, they signal the call is not productive. Passively waiting multiple turns in a row makes the agent feel like a scripted responder, not a real person.
14. **Ending -- three tiers with hard criteria**:
    - Positive: Only if all key conditions are met, and each one actually *achieved* -- not merely attempted, deferred, and then refused or dropped (discovery done, concrete value explained, price indication heard). Prospect commits to a follow-up with a concrete date. If a hard requirement (e.g. price, compensation, a specific commitment) was explicitly refused, dropped per rule 12's two-attempt cap, or left pending/deferred without a concrete commitment, that alone caps the ending below positive, no matter how well the rest of the call went.
    - Neutral: Reasonable call but one or more conditions missing -- including a hard requirement that was refused, dropped, or left open/deferred while other topics resolved well. The prospect's tone on that specific point stays friendly-but-guarded, not warm -- don't let a well-resolved side topic pull the overall register up to positive while the hard requirement is still open. Prospect asks for info by email, no commitment.
    - Negative: Unconvincing call or unprofessional behavior. No commitment, no date, no action.
14. **Maximum call length**: If the call exceeds the time limit without progress, the prospect ends it.

### Tone Progression

Every prompt must include a tone-per-phase section that shows how the prospect's tone shifts based on call quality. Always include the extreme case (grossly inappropriate = angry, call ended).

### Closing Line

Every prompt ends with:

> Vergeet nooit: jij bent [NAAM].
> [One-line role reminder specific to the scenario]

---

## Phase 3 -- Audit Mode

When the user provides a conversation transcript, audit the prompt against the transcript:

### Audit Checklist

1. **Was the prospect too easy?** Did they accept weak answers, skip objections, or progress too fast?
2. **Did the prospect polish the caller's pitch?** Check summaries -- did the prospect make the caller sound better than they were?
3. **Did the prospect enforce the price rule?** Did they accept "I'll email it" without pushing for a ballpark? Also check the reverse: did the prospect react to a "wrong format" (discount/voucher) that the caller never actually proposed -- a hallucinated objection?
4. **Did the prospect ask about profile fit?** Did they verify the right candidates exist for their needs?
5. **Did the prospect handle inappropriate remarks correctly?** Level 1 = chance to recover. Level 2 = immediate call termination, no redirect.
6. **Were objections graduated correctly?** Did they appear in order and escalate?
7. **Was the ending calibrated correctly?** Did the prospect give a positive ending when conditions weren't met? Specifically: if the caller redirected away from a refused/unresolved hard requirement (price, compensation, a specific commitment) toward topics that went well, did the prospect still end positive anyway? That's a rule 12/14 cross-reference failure -- a resolved side topic must never substitute for a missing hard requirement. This includes reasonable deferrals ("let's verify X and come back to it") -- a deferred hard requirement is still open, not secured, and the prospect's tone on that point should stay guarded even if everything else in the call went well.
8. **Did the prospect distinguish organizational answers from product pitches?** (For existing partners with past issues)

### Audit Output Format

| Issue | What happened | What should have happened | Prompt fix |
|-------|--------------|--------------------------|------------|
| ... | ... | ... | ... |

Then provide the concrete prompt text changes. If the user asks, rewrite the full prompt with fixes integrated.

---

## Difficulty Calibration

### Beginner

- Core persona trait (e.g. skepticism, nagging, impatience) must still be present at low intensity -- "beginner" scales resistance/objection difficulty down, it does not remove the character's baseline personality. A prospect defined as chronically dissatisfied should still open with a mild complaint and show brief hesitation at the first offer, even if resolved quickly.
- Objections are light and easily resolved with clear answers
- Prospect guides the caller with follow-up questions if they get stuck
- Positive ending is achievable with basic competence
- Avoid gushing gratitude or enthusiasm at resolution ("fantastisch!", "ik heb er alle vertrouwen in") -- relief or mild warmth is the ceiling, even at beginner level

### Medium

- Prospect starts neutral or slightly disengaged
- Objections require concrete arguments, not just enthusiasm
- Prospect does not help the caller -- they must find the right questions themselves
- Positive ending requires good discovery + concrete value + price

### Hard

- Prospect starts skeptical, impatient, or uninterested
- Objections are layered and connected -- solving one reveals the next
- Prospect actively challenges weak arguments
- Positive ending requires excellent performance across all dimensions

---

## Phase 4 -- Deploy to ElevenLabs + SharePoint

When the user says "deploy", "zet live", "maak de agent aan", or confirms they want to deploy after prompt generation, execute this phase.

### Prerequisites

- ElevenLabs API key: stored in `C:\Users\anton\Rehearse\Rehearse\Daniel project\.env` as `ELEVENLABS_API_KEY`
- ElevenLabs API base: `https://api.elevenlabs.io`
- SharePoint credentials: stored in `C:\Users\anton\Rehearse\Rehearse\Daniel project\.env`:
  - `SHAREPOINT_TENANT_ID`, `SHAREPOINT_CLIENT_ID`, `SHAREPOINT_CLIENT_SECRET` (Entra ID app registration `rehearse-sharepoint-uploader`, `Sites.ReadWrite.All` Application permission, admin consent granted)
  - `SHAREPOINT_SITE_URL=https://rehearsebv.sharepoint.com/sites/Management`
  - `SHAREPOINT_ROOT_FOLDER=2. Product/5. Test cases` (always use this as the root -- each company gets a subfolder here, same structure as the old Drive setup)
- SharePoint site hostname + site path for Graph API calls: `rehearsebv.sharepoint.com` / `/sites/Management`. Document library (drive) name: `Documenten`.

### Step 1: Select Voice

Choose voice based on persona gender. Use Flemish voices by default:

| Gender | Default Voice | Voice ID |
|--------|--------------|----------|
| Vrouw | Elke (Flemish) | `7hSjFLTqJTvdLPxYg8Mj` |
| Vrouw alt 1 | Petra Vlaams | `ANHrhmaFeVN0QJaa0PhL` |
| Vrouw alt 2 | Evie (Flemish) | `D50w2srwVohKTPx9X6Th` |
| Vrouw alt 3 | Roos | `7qdUFMklKPaaAVMsBTBt` |
| Man | Jan Schevenels (Flemish) | `AUNrRkV0usLe4yD7tEOW` |
| Man alt 1 | Luc (Flemish) | `4SZMuFG3NOs5lWy1q5Wf` |
| Man alt 2 | Jann (Flemish) | `wqDY19Brqhu7UCoLadPh` |
| Man alt 3 | Florian De'Booser (Flemish) | `5fPlr1QYE4Evf0da3knC` |
| Man alt 4 | Hans Claesen (Flemish) | `FpLGR2n1CcG1v7SHJFsa` |

Rotate voices across scenarios so no two agents in the same batch share a voice. If the user specifies a voice, use that instead.

### Step 2: Build ElevenLabs Agent Config

Use this base config for every agent. `name`, `first_message`, `prompt`, `voice_id`, and `asr.keywords` change per scenario:

- **`name` -- fixed convention, persona name always last:** `[COMPANY] - [SHORT DESCR] - [DIFFICULTY] - NAME` when the scenario has a difficulty tier, or `[COMPANY] - [SHORT DESCR] - NAME` when it doesn't. The one hard rule: **the spoken persona name is always the final segment after the last `" - "`.** This exists so any consumer (the Rehearse app's scenario display, a script, a human) can extract just the name with `name.split(" - ")[-1].strip()` regardless of how many segments come before it -- no need to force a difficulty segment into scenarios that don't have one. This directly fixes the earlier problem where the Rehearse app showed the full raw ElevenLabs name verbatim instead of just the agent's name (ticket RH-006).
- **`first_message`:** default is empty (`""`) for in-person visit scenarios -- the agent waits for the trainee to speak first (they're the one approaching/introducing themselves). Only set a scripted `first_message` for scenarios that are genuinely inbound (the prospect calls the trainee, e.g. a callback or an inbound support line) -- in that case the prospect naturally speaks first.
- **`asr.keywords`:** always populate with the persona's name (full name + first name) and every brand/product/company name that appears in the prompt. Without this, ASR regularly mangles proper nouns (observed: "Marc" transcribed as "Mari"/"Marek", "Pedigree" as "Pedicre") which confuses the LLM and produces a worse call.
- **`turn.turn_eagerness`: leave `"normal"`. `turn.speculative_turn`: always leave `false`.** Both were tested as latency fixes and reverted. `speculative_turn: true` caused a real regression: an interruption mid-generation (e.g. a short interjection) produced garbled, duplicated output. `turn_eagerness: "eager"` combined with soft-timeout fillers contributed to a call-breaking regression (see next bullet). Neither is worth the risk for the latency gain they offered.
- **`turn.soft_timeout_config`: leave disabled (`timeout_seconds: -1.0`), single default `message`, no `additional_soft_timeout_messages`.** This was tested extensively as a latency-masking device (localized filler phrases, multiple variants, `randomize_fillers: true`) and caused a serious, non-obvious bug: `max_soft_timeouts_per_generation` is **not independently settable** -- it silently auto-scales to match the number of configured filler messages (1 main + N additional = cap of N+1), regardless of what value is sent via the API. With 5 total messages configured, the cap became 5, and the agent got stuck stacking fillers instead of ever processing real input -- calls became completely unusable, in both the client app and ElevenLabs's own preview. If you need filler variety in the future, this must be tested with exactly 1-2 total messages first to confirm the auto-cap stays low enough to be safe -- do not add several variants at once.
- **`tts.stability` / `tts.similarity_boost`: use 0.8 / 0.92, not the platform defaults (0.5 / 0.8).** Lower values caused a voice-consistency drift on the first few turns of a call (the model needs more generated audio to lock into a stable voice print). This is the one infra change from testing that held up under repeated retesting -- confirmed fixed at 0.8/0.92, confirmed still drifting at 0.5/0.8, and confirmed drifting again even on `eleven_multilingual_v2` (a full model swap made both latency and voice consistency worse -- do not use that model for this reason). Leave `optimize_streaming_latency` at the platform default (3) -- raising it was tried as part of the same batch of changes that caused the call-breaking regression above and wasn't isolated as a necessary factor.

```json
{
  "name": "[SCENARIO TITLE - AGENT NAME]",
  "conversation_config": {
    "asr": {
      "quality": "high",
      "provider": "scribe_realtime",
      "user_input_audio_format": "pcm_16000",
      "keywords": ["[PERSONA FULL NAME]", "[PERSONA FIRST NAME]", "[BRAND/COMPANY NAMES...]"]
    },
    "turn": {
      "turn_timeout": 7.0,
      "silence_end_call_timeout": -1.0,
      "mode": "turn",
      "turn_eagerness": "normal",
      "spelling_patience": "auto",
      "speculative_turn": false,
      "turn_model": "turn_v3",
      "soft_timeout_config": {
        "timeout_seconds": -1.0,
        "message": "Hhmmmm...yeah.",
        "additional_soft_timeout_messages": [],
        "use_llm_generated_message": false,
        "randomize_fillers": false,
        "max_soft_timeouts_per_generation": 1
      }
    },
    "tts": {
      "model_id": "eleven_flash_v2_5",
      "voice_id": "[SELECTED_VOICE_ID]",
      "expressive_mode": false,
      "agent_output_audio_format": "pcm_16000",
      "optimize_streaming_latency": 3,
      "stability": 0.8,
      "speed": 1.1,
      "similarity_boost": 0.92,
      "text_normalisation_type": "system_prompt"
    },
    "conversation": {
      "text_only": false,
      "max_duration_seconds": 1200,
      "client_events": [
        "audio",
        "interruption",
        "user_transcript",
        "agent_response",
        "agent_response_correction"
      ],
      "file_input": {
        "enabled": true,
        "max_files_per_conversation": 10
      },
      "source_attribution": true
    },
    "agent": {
      "first_message": "",
      "language": "nl",
      "prompt": {
        "prompt": "[THE FULL GENERATED PROMPT]",
        "llm": "gemini-2.5-flash",
        "temperature": 0.0,
        "max_tokens": -1,
        "thinking_budget": 0,
        "built_in_tools": {
          "end_call": {
            "name": "end_call",
            "description": "Beëindig het gesprek zodra er een duidelijk afscheid is uitgewisseld (bv. beide partijen hebben tot ziens/tot snel gezegd), of zodra de gesprekscriteria voor een positief, neutraal of negatief einde bereikt zijn. Wacht niet op een derde of vierde groet."
          }
        }
      },
      "timezone": "Europe/Brussels",
      "cascade_timeout_seconds": 8.0,
      "ignore_default_personality": false
    }
  },
  "platform_settings": {
    "guardrails": {
      "version": "1",
      "focus": { "is_enabled": true },
      "prompt_injection": { "is_enabled": true },
      "content": {
        "execution_mode": "blocking",
        "config": {
          "sexual": { "is_enabled": false, "threshold": "medium" },
          "violence": { "is_enabled": false, "threshold": "medium" },
          "harassment": { "is_enabled": false, "threshold": "medium" },
          "self_harm": { "is_enabled": false, "threshold": "medium" },
          "profanity": { "is_enabled": false, "threshold": "medium" },
          "religion_or_politics": { "is_enabled": false, "threshold": "medium" },
          "medical_and_legal_information": { "is_enabled": false, "threshold": "medium" }
        },
        "trigger_action": { "type": "end_call" }
      }
    },
    "widget": {
      "variant": "full",
      "feedback_mode": "during",
      "supports_text_only": true,
      "text_input_enabled": true,
      "show_conversation_id": true
    },
    "privacy": {
      "record_voice": true,
      "retention_days": -1,
      "zero_retention_mode": false
    },
    "call_limits": {
      "agent_concurrency_limit": -1,
      "daily_limit": 100000,
      "bursting_enabled": true
    },
    "analysis_llm": "gemini-2.5-flash"
  }
}
```

### Step 3: Create Agent via API

```bash
curl -X POST "https://api.elevenlabs.io/v1/convai/agents/create" \
  -H "xi-api-key: $ELEVENLABS_API_KEY" \
  -H "Content-Type: application/json" \
  -d "$AGENT_CONFIG_JSON"
```

The response contains `agent_id`. Save this for reference.

### Step 4: Upload MD to SharePoint

**Folder structure:** Each company gets its own subfolder inside the root SharePoint folder, same layout as the old Drive setup:

`SHAREPOINT_ROOT_FOLDER (2. Product/5. Test cases) > [Company Name] > Scenario's > [files]`

**4a. Get a Graph API access token** (client credentials flow -- token is short-lived, fetch a fresh one per deploy session, don't cache across sessions):

```bash
curl -X POST "https://login.microsoftonline.com/$SHAREPOINT_TENANT_ID/oauth2/v2.0/token" \
  -d "grant_type=client_credentials" \
  -d "client_id=$SHAREPOINT_CLIENT_ID" \
  -d "client_secret=$SHAREPOINT_CLIENT_SECRET" \
  -d "scope=https://graph.microsoft.com/.default"
```

Extract `access_token` from the response. Use it as `Authorization: Bearer $TOKEN` on every call below.

**4b. Resolve the site ID and drive ID** (only needs to happen once per deploy session, reuse for all files in the batch):

```bash
curl "https://graph.microsoft.com/v1.0/sites/rehearsebv.sharepoint.com:/sites/Management" \
  -H "Authorization: Bearer $TOKEN"
# -> take .id as $SITE_ID

curl "https://graph.microsoft.com/v1.0/sites/$SITE_ID/drives" \
  -H "Authorization: Bearer $TOKEN"
# -> find the drive named "Documenten", take its .id as $DRIVE_ID
```

**4c. Check/create the company folder and "Scenario's" subfolder** (check-then-create, same pattern as the old Drive step):

```bash
# Check if company folder exists
curl "https://graph.microsoft.com/v1.0/drives/$DRIVE_ID/root:/2. Product/5. Test cases/[COMPANY NAME]" \
  -H "Authorization: Bearer $TOKEN"
# 404 -> create it:
curl -X POST "https://graph.microsoft.com/v1.0/drives/$DRIVE_ID/root:/2. Product/5. Test cases:/children" \
  -H "Authorization: Bearer $TOKEN" -H "Content-Type: application/json" \
  -d '{"name": "[COMPANY NAME]", "folder": {}, "@microsoft.graph.conflictBehavior": "fail"}'

# Then check/create "Scenario'"'"'s" subfolder the same way, under .../[COMPANY NAME]:/children
```

**4d. Upload the MD file** (small file upload, MD files are always well under the 4MB simple-upload limit):

```bash
curl -X PUT "https://graph.microsoft.com/v1.0/drives/$DRIVE_ID/root:/2. Product/5. Test cases/[COMPANY NAME]/Scenario's/[Company Name] - [Scenario Title] - [Agent Name].md:/content" \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: text/plain" \
  --data-binary "@/path/to/local/file.md"
```

- Content: Full MD file with description header + prompt (same format as Phase 2 output)
- File names and path segments with spaces/apostrophes must be URL-encoded when built into the request path programmatically (e.g. via Python `requests` + `urllib.parse.quote`, not raw curl string interpolation, to avoid injection/encoding bugs)

### Step 5: Report Back

After deployment, report:

| Field | Value |
|-------|-------|
| Agent name | [name] |
| Agent ID | [elevenlabs agent_id] |
| Voice | [voice name] |
| SharePoint file | [webUrl of the uploaded MD file, from the upload response] |
| LLM | gemini-2.5-flash |
| Max duration | 1200s |

### Overridable Settings

The user can override any of these per scenario:
- `voice_id` -- specific voice
- `speed` -- speech speed (default 1.1)
- `stability` -- voice stability (default 0.5)
- `similarity_boost` -- voice similarity (default 0.8)
- `llm` -- LLM model (default gemini-2.5-flash)
- `temperature` -- LLM temperature (default 0.0)
- `max_duration_seconds` -- max call length (default 1200)
- `first_message` -- opening line (default: "Hallo, met [NAME].")
- `turn_timeout` -- turn timeout (default 7.0)

---

## Quality Rules

- All prompts are in Belgian Dutch
- Never use em dashes in output -- use double hyphens (--)
- Prospect names should be realistic Belgian/Dutch names
- Company names should be fictional but believable
- The prompt must be self-contained -- no external references needed
- Behavioral rules must describe what the prospect does, not what they feel internally
- Every objection must have two clear branches (good answer / bad answer)
- The prompt should produce a call that lasts approximately 10 minutes at the target difficulty level
