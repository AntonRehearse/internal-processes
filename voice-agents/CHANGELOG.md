# Voice Agent Prompt Builder -- Changelog

Log of learnings from testing AI voice agents, folded back into the skill so future agents inherit the fix automatically. Only generalizable findings go here -- scenario-specific fixes (e.g. one agent's specific objection wording) stay in that agent's own prompt.

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
