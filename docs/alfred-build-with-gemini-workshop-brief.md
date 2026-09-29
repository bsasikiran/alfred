# Alfred — Build with Gemini workshop brief

Date: Tuesday, 29 September 2026 | Munich/Ismaning | Track 3: App Builders
Status: implementation brief, not a claim that integrations already work.

## North star

**Alfred — The Family Timekeeper**

*Less time coordinating. More time together.*

Alfred brings each family member's commitments into one household plan, spots conflicts, suggests practical next steps, and prepares a concise daily briefing. The prototype must show planning intelligence rather than simply display a shared calendar.

## One-sentence MVP

Given a small, fictional four-person household schedule and one new commitment in natural language, Alfred identifies a conflict or preparation need, proposes a workable change, and produces a family briefing; it saves nothing without explicit confirmation.

## The demo story

Use fictional people: Alex (parent), Sam (parent), Mia (child), Leo (child). All times below are local Europe/Berlin on 29 September 2026. Seed: Alex has a work call 16:00–17:00; Sam has an appointment 16:30–17:30; Mia has football 17:00–18:00 at the sports ground, requiring a 20-minute handoff/travel buffer; Leo is home after school from 15:30. A family dinner is pencilled in for 18:30. Give Alex a flexible 20-minute chore, 'put out recycling', before 20:00. Treat the example as fictitious; if the schedule proves physically impossible, Alfred should flag it rather than invent transport or childcare.

User asks: 'Mia's coach moved football to 16:45. Can we still make it, and when should recycling be done?'

Expected: cite the overlapping parental commitments and handoff/travel constraints; present a safe proposed plan (e.g., ask who can take Mia rather than silently assigning Leo or assuming an adult is free); suggest a free 20-minute chore slot only if the sample schedule supports it; provide a short spoken-style briefing. Offer a 'Review changes' action; don't write to a real calendar.

## Scope: ship in this order

1. Load editable fictional events, profiles, chores and preferences; retain source and status on every item.
2. Implement read-only tools: list household commitments by day, find overlaps/free slots, and obtain household preferences. Use ordinary deterministic code to check times and travel buffers; let the model explain the result.
3. Implement the hero path: natural-language request → tool reads → conflict and proposal cards → daily briefing.
4. Add a single review/confirm path to a sandbox data store only if core demo works. Make the proposed plan visibly different from confirmed events.
5. Deploy and run the hero demo from the deployed URL. Polish only after that.

Not in the three-hour MVP: four real Google OAuth calendar connections, email scraping, school portal integration, Nest speaker control, automatic schedule changes, robust notifications, payment, or a multi-agent hierarchy. Keep them on a roadmap slide, not the critical path.

## Suggested data contract

```json
{
  "household_id": "demo-family",
  "timezone": "Europe/Berlin",
  "profiles": [
    {"id": "alex", "role": "parent"},
    {"id": "sam", "role": "parent"},
    {"id": "mia", "role": "child"},
    {"id": "leo", "role": "child"}
  ],
  "commitments": [
    {
      "id": "event-1",
      "person_ids": ["mia"],
      "title": "Football",
      "start": "2026-09-29T16:45:00+02:00",
      "end": "2026-09-29T17:45:00+02:00",
      "place": "Sports ground",
      "source": "demo input",
      "status": "proposed",
      "travel_buffer_minutes": 20
    }
  ],
  "chores": [
    {"id": "chore-1", "title": "Put out recycling", "duration_minutes": 20, "due": "2026-09-29T20:00:00+02:00", "assigned_to": null}
  ]
}
```

Store original football time as its own confirmed event and the moved time as a proposal until reviewed. For any production design, access to children's and adults' data must be governed per household and account; the demo should avoid real family data.

## Agent behavior contract

System/developer prompt to adapt to the supplied workshop starter:

> You are Alfred, a practical household planning assistant. Your job is to reduce coordination work, not to invent certainty. Read household data through provided tools and use the Europe/Berlin timezone. Distinguish confirmed events, user-provided updates and suggestions. Identify overlapping events and travel/preparation needs. Do not infer an available driver, guardian, consent, or calendar write from a missing record. If constraints cannot be satisfied, say so and ask for the smallest decision needed. Offer up to two concrete alternatives and a brief, natural daily spoken summary. Never create or modify an event without an explicit confirmation step. Never reveal one person's private details to another without appropriate permission.

Useful tool interfaces: `get_day(date, profile_ids)`, `get_preferences(household_id)`, `find_free_slots(date, person_ids, duration_minutes, window)`, `detect_conflicts(proposed_commitment)`. If implementing writes, use `save_confirmed_change(change_id)` only behind a separate UI confirmation, not from a model suggestion alone.

Output shape for UI (adapt to actual workshop framework):

```json
{
  "headline": "Football moved earlier; transport needs a decision",
  "conflicts": [{"event_ids": ["event-1", "event-2"], "reason": "...", "severity": "needs_decision"}],
  "proposals": [{"title": "...", "requires_confirmation": true, "assumptions": []}],
  "briefing_text": "...",
  "unresolved_questions": ["Who can take Mia to football?"]
}
```

Structured output can make UI rendering predictable, but validate facts, times, permissions and business logic in code. Function calling requests tool execution; your app actually executes the tools. See official Gemini docs: https://ai.google.dev/gemini-api/docs/generate-content/structured-output and https://ai.google.dev/gemini-api/docs/function-calling .

## Tomorrow's run of show

- 09:30–10:30: check in, confirm Track 3 room, open sandbox instructions, note project URL/repo/credentials without publishing secrets; have registration confirmation and photo ID.
- 10:30–11:15: capture the starter agent's architecture, supported tools, model and deployment route. Avoid building a parallel stack before seeing the lab.
- 11:15–12:00: run starter exactly once, copy seed fixture, establish one end-to-end local demo.
- 12:00–13:00: lunch is scheduled inside the builder block; budget build time accordingly. Sketch UI/cards and ask an expert one blocker question if needed.
- 13:00–14:00: add the two read tools and conflict logic, then the hero scenario.
- 14:00–14:35: wire frontend, status labels and confirmation boundary; deploy early.
- 14:35–15:15: test from deployed URL, resolve biggest bug, rehearse a 90-second demo and capture screenshot/URL.
- 15:15–16:00: showcase and closing; networking until 17:00.

These are personal timeboxes, not a promise about exact lab sequencing. Official agenda: https://cloud.google.com/events/build-with-gemini-munich .

## Acceptance checks

- Four fictional profiles and at least five commitments appear in the UI.
- Football time change exposes the transport conflict without assuming childcare or availability.
- A chore slot is proposed only after checking duration and availability.
- Suggested, confirmed and unresolved are visibly distinct.
- Briefing matches the displayed facts and can be read aloud, even if no speaker is connected.
- End-to-end request works from the deployed frontend; no secrets or actual family data in demo.

## 90-second demo script

'Families already have calendars. Their real problem is turning fragmented commitments into a workable day. Meet Alfred, the Family Timekeeper. Here are four fictional profiles and today's commitments. Mia's football has just moved earlier. I ask Alfred whether we can still make it and when to do a chore. Alfred checks the schedule, catches the parental conflict and travel buffer, and tells us what decision is needed instead of quietly rewriting everyone's calendar. It proposes a slot for the chore and prepares a short household briefing. Only after a person approves would a real integration update a calendar. The outcome is less coordination overhead and more time together.'

## Fallbacks

- If Cloud provisioning stalls: run the starter in the provided local/lab preview and demo the hero scenario there; don't burn the entire session on deploy.
- If custom tools are difficult: feed a small fixed JSON fixture to the agent; write overlap detection as a simple backend function or display verified constraints in the UI.
- If frontend scaffolding stalls: use the provided agent UI, show structured JSON plus a simple briefing panel.
- If speaker integration is unavailable: show `briefing_text` and use browser speech only as optional presentation polish; do not claim Nest support.

## Tonight: 15-minute prep

- Pack laptop and charger; verify registration email and photo ID.
- Test the event's device checklist linked from the official page on the laptop you will bring.
- Copy this brief locally so it is accessible if Wi-Fi is unreliable.
- Do not move real family calendars or children's information into a shared workshop sandbox.
