---
name: Exec
description: Use when building AI-powered roleplay scenarios for skills training, assigning practice exercises to teams, tracking skill proficiency and performance analytics, integrating with LMS platforms, or managing structured training programs with multiple components and cohorts.
metadata:
    mintlify-proj: exec
    version: "1.0"
---

# Exec Skill Playbook

## Product Summary

Exec is an AI-powered roleplay training platform that helps teams practice high-stakes conversations in a safe environment. Agents use Exec to create realistic scenarios with AI characters, assign them to learners with deadlines and completion criteria, track skill proficiency through analytics, and integrate training into broader programs or LMS platforms.

**Key files and locations:**
- API base URL: `https://api.exec.com/rest/v1/`
- API keys: Settings > API (create and manage in workspace)
- Scenarios: Built in Scenario Studio (AI-powered builder)
- Analytics: Roleplays > Analytics or Skills dashboard
- Integrations: Settings > Integrations (LTI 1.3, SSO, iframe embedding)

**Primary docs:** https://docs.exec.com

---

## When to Use This Skill

Reach for Exec when:

- **Building training scenarios** — Create custom roleplay scenarios for sales, support, coaching, or management conversations using the AI-powered Scenario Studio
- **Assigning practice exercises** — Push scenarios to individuals or groups with due dates, minimum passing scores, and attempt limits
- **Tracking skill development** — Monitor proficiency across your team using weighted scoring that emphasizes recent practice
- **Integrating with LMS** — Connect Exec to Canvas, Blackboard, Moodle, or other LTI 1.3-compatible platforms for single sign-on and automatic grade passback
- **Running cohort training** — Bundle roleplays, videos, surveys, and coaching into structured Programs with shared timelines
- **Analyzing team performance** — Pull session data, transcripts, and scores via API or dashboard analytics
- **Managing knowledge resources** — Organize training materials in Knowledge Hub and attach them to scenarios for context

---

## Quick Reference

### API Authentication

```bash
# Create API key in Settings > API, then use in Authorization header
Authorization: Bearer exec_live_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# Example request
curl -X GET "https://api.exec.com/rest/v1/workspace" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

### Core API Endpoints

| Endpoint | Purpose |
|----------|---------|
| `GET /workspace` | Get workspace info |
| `GET /scenarios` | List scenarios (filter by skill, collection, etc.) |
| `POST /scenarios/{id}/access` | Grant user access to scenario |
| `GET /sessions` | List roleplay sessions with scores and transcripts |
| `GET /skills/{id}/proficiency` | Get skill proficiency by user (time-decay weighted) |
| `POST /scenario-studio/job` | Create scenario asynchronously via API |
| `GET /scenario-studio/job/{id}` | Poll scenario creation status |

### Scenario Building Methods

| Method | Best For | Time |
|--------|----------|------|
| **Chat/Text** | Detailed prompts, attaching Knowledge Hub sources | 1-2 min |
| **Voice Setup** | Brainstorming, beginner-friendly | 2-3 min |
| **Templates** | Quick builds from conversation templates | 1 min |

### Assignment Completion Criteria

| Criterion | How It Works |
|-----------|-------------|
| **Minimum score** | Best score across all attempts must meet rank (Bronze 50%+, Silver 75%+, Gold 90%+) |
| **Minimum attempts** | Learner must complete at least N sessions |
| **Maximum attempts** | Assignment locks after N sessions; if score not met, status = Did Not Pass |

### Skill Proficiency Tiers

| Tier | Score | Meaning |
|------|-------|---------|
| Excellent | 90+ | Consistent mastery |
| Proficient | 75–89 | Solid, reliable capability |
| Developing | 50–74 | Building competency |
| Needs Work | <50 | Needs attention |
| Insufficient Data | <3 observations | Not enough practice |

### Rate Limits

- **Burst:** 60 requests/minute
- **Sustained:** 1,000 requests/day
- **Scenario creation:** 10 requests/minute
- **Rate-limited response:** HTTP 429 with `retry_after` field

---

## Decision Guidance

### When to Use Program vs. Course

| Need | Use Program | Use Course |
|------|-------------|-----------|
| Cohort moving together on fixed dates | ✓ | |
| Self-paced, always-available training | | ✓ |
| Repeating batches (e.g., new hires) | Clone program per cohort | Build once, enroll each batch |
| Includes 1:1 coaching or group meetings | ✓ | |
| Includes quizzes or slides | | ✓ |

**Rule of thumb:** If you're cloning the same program every few weeks and only changing dates/roster, move it to a Course.

### When to Edit vs. Remix vs. Clone

| Action | When to Use | Result |
|--------|------------|--------|
| **Edit** | Update a live scenario (fix character, adjust grading) | Changes apply to all future sessions; session history preserved |
| **Remix** | Create a variation (harder version, part two, different audience) | New independent scenario; original stays intact |
| **Clone** | Make a one-off copy for independent use | Exact copy; no connection to original |

### When to Use Knowledge Hub Sources in Scenarios

| Situation | Action |
|-----------|--------|
| Building from your own materials (call transcripts, frameworks, playbooks) | Click Source > Browse Knowledge Hub, select resources, prompt agent to use them |
| Scenario needs to reflect your actual methodology | Attach your CLEAR framework, discovery guide, or call examples |
| Multiple scenarios need the same content | Create Knowledge Hub page once, attach to multiple scenarios |
| Content changes frequently | Update page in Knowledge Hub; all attached scenarios stay in sync |

---

## Workflow

### Building and Assigning a Scenario

1. **Open Scenario Studio** — Click Roleplays > + Create (or + shortcut)
2. **Think through the Four Pillars** — Situation (who, what type of call), Goal (what should happen), Definition of Good (what does strong look like), Challenge (what makes it hard)
3. **Choose a build method** — Chat/text (most common), Voice Setup (brainstorm), or Templates (quick start)
4. **Describe your scenario** — Be brief or detailed; the agent asks clarifying questions
5. **Review the draft** — Check Scenario Details (objective, criteria), Character Details (personality, backstory), Session Settings (language, webcam, cold call)
6. **Test with Try Now** — Run a short test conversation; adjust if needed
7. **Publish** — Make it Workspace (everyone) or Restricted (specific people)
8. **Assign to team** — Click Assign, select people/groups, set due date, minimum score, attempt limits, optional custom message
9. **Track progress** — Monitor from Roleplays > Assignments page

### Pulling Session Data via API

1. **Create API key** — Settings > API > Create API Key (copy immediately, shown only once)
2. **List sessions** — `GET /sessions` with filters (user_emails, scenario_ids, start_date, end_date)
3. **Get session detail** — `GET /sessions/{session_id}` for transcript, scores, feedback
4. **Get scenario analytics** — `GET /scenarios/{id}/analytics-summary` for aggregate performance
5. **Track skill proficiency** — `GET /skills/{id}/proficiency` for weighted scores by user

### Setting Up LTI 1.3 Integration

1. **Navigate to Settings > Integrations > LTI 1.3**
2. **Copy endpoint URLs** — Four URLs your LMS needs (OIDC login, launch, JWKS, OpenID config)
3. **Register in LMS** — Use dynamic registration (one-time URL) or manual connection (paste platform details)
4. **Add scenarios as LMS activities** — In LMS course editor, add external tool, select Exec, paste scenario_id custom parameter
5. **Enable grade passback** — Mark activity as graded; Exec posts scores to LMS gradebook automatically

### Creating a Program

1. **Click Programs > Create Program**
2. **Name it and set launch date** — Optionally link a certification
3. **Add components** — Click Add Component, choose type (Roleplay, Video, Survey, Coaching, etc.), set dates and participants
4. **Enroll participants** — Add by email; optionally assign seats automatically
5. **Launch** — Program moves to Live; participants get enrollment email
6. **Track progress** — Monitor completion, send reminders, view analytics

---

## Common Gotchas

- **API keys shown only once** — Copy immediately after creation. If lost, create a new key; old one cannot be recovered.
- **Switching personality traits erases custom descriptions** — If you toggle between custom and structured traits, your bullet points disappear. Prompt the agent to restore them if needed.
- **Minimum score + maximum attempts mismatch** — Requiring Gold (90%+) with only 1 attempt is unrealistic. Give learners enough attempts to improve.
- **Knowledge Hub sources not guaranteed to appear** — If something specific must be in the scenario, tell the agent explicitly.
- **Proficiency requires 3+ observations** — Skill proficiency scores don't calculate until a user has at least 3 sessions or calls. Fewer than 3 shows "Insufficient Data."
- **Program-based assignments cannot be deleted individually** — They're managed through the program. Delete from the program instead.
- **Microphone permissions block roleplay** — Most "won't load" issues are microphone permission problems. Chrome is the recommended browser.
- **Scenario changes apply to future sessions only** — Editing a live scenario doesn't retroactively change past session scores. Use Remix if you need a separate version.
- **Rate limits on scenario creation** — Only 10 scenario creation requests per minute. Batch jobs or use callbacks for async processing.
- **Email typos prevent login** — If a user can't log in, check Settings > Users for email character-by-character. Extra letters or wrong domain are common.

---

## Verification Checklist

Before submitting work with Exec:

- [ ] **Scenario is published** — Not in draft; visibility is set (Workspace or Restricted)
- [ ] **Assignment criteria are realistic** — Minimum score + maximum attempts give learners a fair chance
- [ ] **API key is secure** — Not committed to code; stored in environment variables or secrets manager
- [ ] **Knowledge Hub sources are published** — Drafts won't be available to scenarios
- [ ] **LTI custom parameter is correct** — Copied from scenario detail page, pasted into LMS activity
- [ ] **Program has at least one component and participants** — Required to launch
- [ ] **Skill proficiency filters are correct** — Using user_ids or user_emails, not mixing both without intent
- [ ] **Roleplay session loaded successfully** — Microphone permissions granted, Chrome browser used
- [ ] **Transcript and scores are visible** — Session completed with End button (not X/close)
- [ ] **Analytics filters match intent** — Date range, scenario, group, or skill filters are set correctly

---

## Resources

**Comprehensive page navigation:** https://docs.exec.com/llms.txt

**Critical documentation:**
- [Scenario Studio (AI-powered building)](https://docs.exec.com/roleplays/ai-agent)
- [API Overview & Quickstart](https://docs.exec.com/api-reference/overview)
- [Assign Scenarios & Track Assignments](https://docs.exec.com/roleplays/assign-scenarios)
- [Programs Overview](https://docs.exec.com/programs/overview)
- [LTI 1.3 Integration](https://docs.exec.com/lti/overview)
- [Skills & Proficiency Tracking](https://docs.exec.com/skills/overview)
- [Knowledge Hub](https://docs.exec.com/knowledge-hub/overview)

---

> For additional documentation and navigation, see: https://docs.exec.com/llms.txt