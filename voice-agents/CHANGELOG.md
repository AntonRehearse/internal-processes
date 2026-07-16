# Voice Agent Prompt Builder -- Changelog

Log of learnings from testing AI voice agents, folded back into the skill so future agents inherit the fix automatically. Only generalizable findings go here -- scenario-specific fixes (e.g. one agent's specific objection wording) stay in that agent's own prompt.

---

## 2026-07-08 -- Fixed naming convention so the persona name can always be extracted

**Problem:** The Rehearse app displays the ElevenLabs agent's raw `name` field verbatim as the scenario title, which is why RH-006 ("scenario display name too long") happened in the first place -- names like `MARS Belgium - De altijd-zagende-verantwoordelijke - Marc Deceuninck (Beginner)` showed in full instead of just "Marc Deceuninck". 11 of the 14 live agents also had no company/description prefix at all (just the bare persona name, e.g. "Bart", "Kevin"), so there was no consistent structure to parse in the first place.

**Fix:** Adopted a fixed convention: `[COMPANY] - [SHORT DESCR] - [DIFFICULTY] - NAME` (with a difficulty tier) or `[COMPANY] - [SHORT DESCR] - NAME` (without one). The one invariant: **the persona name is always the last segment after the final `" - "`**, so extraction is `name.split(" - ")[-1].strip()` regardless of how many segments precede it -- no need to force an empty difficulty segment into scenarios that don't have one.

Renamed all 14 live agents to this convention (metadata-only change via `PATCH /v1/convai/agents/{id}` on the `name` field -- no prompt, TTS, or turn config touched). Added to the skill's Phase 4 naming guidance so every future agent follows it from creation.

**How to apply:** Any new agent gets this name format from the start. If the Rehearse app's display-name logic gets fixed to use the actual persona field instead of the raw ElevenLabs name (the deeper fix behind RH-006), this convention becomes a safety net rather than a requirement -- but it costs nothing to keep either way.

---

## 2026-07-08 -- Final validated TTS/turn config (supersedes the round-2/round-3 entries above)

**This entry corrects the Phase 4 base config guidance.** The earlier infra-tuning entries in this log document real intermediate states, but the round-2 "eager + multi-filler" config was never actually safe -- it's what caused the call-breaking regression (`max_soft_timeouts_per_generation` silently auto-scaling to 5). After a full revert and isolated re-testing, here is what's actually confirmed, tested across multiple transcripts:

**Confirmed fix (keep):**
- `tts.stability: 0.8`, `tts.similarity_boost: 0.92` -- this alone resolves the early-call voice-consistency drift. Tested clean at these values, tested still-drifting at platform defaults (0.5/0.8), tested still-drifting even on a full model swap to `eleven_multilingual_v2` (which also made latency worse -- don't use that model). This is the only infra change from today that survived every retest.

**Confirmed NOT to use (revert/avoid):**
- `turn_eagerness: "eager"` and `speculative_turn: true` -- both were tried to cut latency. `speculative_turn` caused garbled/duplicated output on interruption. `turn_eagerness: eager` was part of the batch that led to the soft-timeout regression below. Neither isolated latency benefit was ever confirmed to outweigh the risk once retested independently.
- **Multiple soft-timeout filler messages** (`additional_soft_timeout_messages` with several variants + `randomize_fillers: true`) -- `max_soft_timeouts_per_generation` is not independently settable via the API; it silently auto-scales to match the total number of configured filler messages. 5 total messages produced a cap of 5, and the agent got stuck stacking fillers instead of processing input -- calls became completely unusable in both the client app and ElevenLabs's own preview. Leave soft timeout disabled (`timeout_seconds: -1.0`) unless this is retested with exactly 1-2 total messages to confirm the auto-cap stays safe.
- `optimize_streaming_latency` raised from the default (3) -- this was bundled into the same batch as the above and never isolated as a necessary factor. Leave at platform default.

**How to apply:** Use `stability: 0.8` / `similarity_boost: 0.92` as the new default for every future agent (already updated in the skill's Phase 4 base config). Do not enable `turn_eagerness: eager`, `speculative_turn`, multi-message soft timeout, or raised `optimize_streaming_latency` without isolated, single-variable retesting -- bundling several infra changes at once (as happened today) makes it very hard to tell which change caused a regression when one appears.

---

## 2026-07-08 -- Infra tuning: voice consistency, latency, and a speculative_turn regression

**Finding, round 1:** Two infra complaints, unrelated to prompt content: (1) the TTS voice sounded slightly different for the first 1-2 turns of a call before settling, and (2) the agent went visibly silent for a few seconds on longer turns later in the call, with no signal it was still "there."

**Fix, round 1:** Raised `tts.stability` (0.5 -> 0.7) and `tts.similarity_boost` (0.8 -> 0.9) for voice consistency. Enabled `turn.soft_timeout_config` (was disabled, `timeout_seconds: -1`) with a Dutch filler ("Eventjes kijken...") instead of the English default ("Hhmmmm...yeah.") to mask generation delay.

**Finding, round 2 (after retesting):** Voice still drifted on the 3rd utterance specifically -- the round-1 stability bump wasn't enough. Latency was better but still occurred mid/late-call. The single filler message repeated back-to-back when two slow turns landed close together, which read as robotic.

**Fix, round 2:** Pushed `stability` to 0.8 and `similarity_boost` to 0.92. Set `turn_eagerness: "eager"` and enabled `speculative_turn: true` to attack latency further. Added 4 additional Dutch filler variants + `randomize_fillers: true` so the same phrase never repeats back-to-back.

**Finding, round 3 (after retesting again):** Voice consistency and latency both improved significantly -- confirmed fixed. But `speculative_turn: true` introduced a real regression: when the caller made a short interjection ("Au") mid-generation, the agent's response came out garbled -- 5 soft-timeout fillers stacked in one turn, and a sentence that restarted itself mid-word ("een beetje op**Dat zou fantastisch zijn, Anton!** Als je de schappen..."). Separately, the agent fully ignored the "Au" itself and resumed its exact previous sentence as if nothing had been said.

**Root cause:** `speculative_turn` makes the agent start generating a response before the caller's turn is confirmed finished. An interruption event arriving mid-speculative-generation appears to trigger a regeneration that doesn't cleanly discard the prior attempt, producing concatenated/duplicated output including multiple soft-timeout fillers (the max-1-per-generation cap doesn't hold across these regenerations). Separately, no rule existed for handling a caller's short reflexive interjection (a real human would acknowledge "you okay?" before continuing, not blindly resume a cut-off sentence).

**Fix, round 3 (final):**
- Reverted `speculative_turn` to `false` -- the latency win holds without it (comes from `turn_eagerness: eager` + `optimize_streaming_latency: 4`), and reverting removes the glitch.
- Added a new behavioral rule (skill rule 11b) for reacting to short reflexive interjections: acknowledge briefly in character, never resume the exact previous sentence verbatim.
- Updated the skill's Phase 4 base config template to the tuned defaults (`stability: 0.8`, `similarity_boost: 0.92`, `optimize_streaming_latency: 4`, `turn_eagerness: eager`, `speculative_turn: false`, localized multi-message soft timeout with randomization) so every future agent starts from these values instead of the platform defaults.
- Applied to all three Marc Deceuninck tiers' live agents.

**How to apply:** Don't enable `speculative_turn` even to chase latency -- the interruption-handling regression it causes is worse than the delay it saves, and `turn_eagerness: eager` + streaming-latency tuning covers most of the same ground safely. When auditing any agent transcript, also check for reflexive-interjection handling (rule 11b) alongside the existing nonsense/evasive-answer check (rule 11).

---

## 2026-07-08 -- Hallucinated "wrong format" objection + deferral treated as resolved

**Finding:** Third test pass on Marc Deceuninck (Beginner). Two new issues, both surfaced in the same transcript:

1. The rep never proposed a voucher, discount, or percentage -- he asked to verify delivery dates/invoices before deciding on compensation. Marc responded as if the rep *had* proposed a tegoedbon/discount ("Ik dacht dat jullie dat eigenlijk niet mochten doen met tegoedbonnen of kortingen..."), reacting to something that was never said.
2. The rep deferred the compensation decision pending that verification ("we komen er later op terug") -- not a refusal, a reasonable pending item. But the call still closed with full warmth ("Ik ben blij dat we weer op de goede weg zijn," "uitstekend voorstel") once the shelf/frequency/displays topics resolved well, with no signal that compensation was still open.

**Root cause:**
1. Rule 6 ("reaction to wrong compensation proposal") wasn't gated on the caller actually proposing a wrong format -- it fired based on the topic being compensation, not on the specific trigger condition being met.
2. The ending-cap fix from the previous round (see below) only covered explicit refusal or an outright dropped ask. It didn't cover a *reasonably deferred* item -- which is not a stonewall, but is also not secured, and should keep the ending capped and the tone guarded until resolved.

**Fix:**
- Rule 5 (skill: Price rule) now explicitly gates any "wrong format" reaction on the caller actually proposing that wrong format -- a request to verify documentation first is not a trigger.
- Rules 12 and 14 (skill) now cover deferred/pending items, not just explicit refusal: "let's verify X and come back to it" is not a secured outcome, and caps the ending + keeps that point's tone guarded regardless of how reasonable the deferral was.
- Applied to all three Marc Deceuninck tiers' live prompts.

**How to apply:** When auditing any agent with a "wrong proposal format" reaction, verify the reaction is gated on the actual proposed content, not just the topic. When auditing ending calibration, check not just for outright refusal but for reasonable deferrals being incorrectly treated as resolved -- both should cap the ending and keep that point's tone guarded.

---

## 2026-07-08 -- Beginner difficulty was stripping core persona, not just easing it

**Finding:** Testing Marc Deceuninck (MARS Belgium, beginner difficulty -- a chronically dissatisfied/nagging retail contact) showed zero nagging or skepticism at all. The agent was warm, grateful, and enthusiastic from the first line, with no objection resistance and instant, gushing acceptance of every proposal.

**Root cause:** The skill's Difficulty Calibration table defined "Beginner" purely in terms of how easy the objections are to resolve, with no rule requiring the character's baseline personality trait to still show through at low intensity. Result: an agent could pass beginner-difficulty criteria while being a completely different character than intended.

**Fix:** Added to the Beginner section of Difficulty Calibration:
- Core persona trait must still be present at low intensity -- difficulty scales resistance, not personality.
- Capped enthusiasm/gratitude ceiling at resolution (no "fantastisch!" / "ik heb er alle vertrouwen in" for a trait defined as skeptical/nagging).

**How to apply:** When building or auditing any beginner-difficulty agent going forward, check that the persona's defining trait is still audible in the transcript, even though objections resolve easily. If it isn't, that's a skill-level gap, not a one-off scenario bug -- fix it in the skill, not just the agent's own prompt.

---

## 2026-07-08 -- Redirecting away from a refused hard requirement still produced a positive ending

**Finding:** Re-testing Marc Deceuninck (Beginner) after the persona fix above: the rep flatly refused to offer any compensation for the expired stock, twice. Marc correctly pushed back once, got refused again, then dropped it and pivoted to the shelf/frequency/displays topics -- which all resolved well. The call still ended fully positive (follow-up scheduled, warm close), even though the compensation ask -- an explicit hard requirement for a positive ending per rule 14 -- was never met.

**Root cause:** Rule 12 ("never ask the same question more than twice, then let it rest and move on") and rule 14 ("ending tiers with hard criteria") weren't cross-referenced. The model correctly followed rule 12 in isolation (stop probing, move to the next topic) but nothing told it that dropping a *hard-requirement* topic should cap the ending -- so a well-resolved side topic (shelf, frequency) was allowed to substitute for a missing hard requirement (compensation). Checked Medium and Hard tiers too: Hard already tied a dropped point to a negative ending; Medium partially did (generic neutral/negative steer, not compensation-specific); Beginner had no tie-in at all. The gap was real but uneven across tiers -- not caught earlier because only Beginner had been transcript-tested up to that point.

**Fix:** 
- Rule 12 (skill: "Never ask the same question more than twice") now states that if the dropped point is a hard requirement for a positive ending, dropping it caps the ending below positive regardless of how well other topics went.
- Rule 14 (skill: ending tiers) now requires hard-requirement conditions to be *actually achieved*, not merely attempted-then-refused, for a positive ending.
- Audit checklist item 7 now explicitly calls out this failure mode: a caller redirecting away from a refused hard requirement toward topics that go well must not still produce a positive ending.
- Applied directly to all three Marc Deceuninck tiers' live prompts (compensation is the hard requirement in this scenario), not just Beginner.

**How to apply:** Whenever an agent has any hard-requirement objection (price, compensation, a specific commitment) alongside other resolvable topics, check during audit whether a refused/dropped hard requirement can be "outrun" by resolving everything else. If the transcript shows a positive ending despite an unresolved hard requirement, that's this exact bug -- fix at the skill level (rules 12 + 14), then propagate to every agent that has a hard-requirement objection, not just the one that surfaced it.

---

## Process note

Iteration loop for any agent, going forward:
1. Test call -> capture transcript
2. Pull the live prompt directly from ElevenLabs (`GET /v1/convai/agents/{agent_id}`) -- not from the SharePoint copy, which can go stale if anyone edits the agent directly in the ElevenLabs UI
3. Run `/voice-agent-prompt-builder` audit mode against the transcript + live prompt
4. If the finding is scenario-specific: fix that agent's prompt only
5. If the finding is generalizable (a gap in the skill's own rules/structure/calibration): edit the skill file directly, and log it here with root cause + fix
6. On approval, `PATCH` the existing `agent_id` with the updated prompt and overwrite the corresponding SharePoint MD in the same step, so ElevenLabs (runtime) and SharePoint (doc trail) never diverge
7. Prompt-only fixes don't need a Planner ticket -- reserve tickets for actual app/code bugs (e.g. scenario display-name truncation, phone-call framing on non-phone scenarios) that require touching the Rehearse codebase itself
