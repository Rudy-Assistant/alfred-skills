---
name: email-composer
description: |
  Draft professional emails with tone control (formal, friendly, urgent, apologetic). Templates for common scenarios: follow-up, introduction, request, thank you, decline, escalation. Support reply drafting from original emails via Gmail MCP. Optimize subject lines and include email etiquette guidelines. Create Gmail drafts directly without sending.
---

# Email Composer â Professional Communication

Draft emails faster with built-in tone control, templates, and seamless Gmail integration. Compose new messages, reply to existing threads, or batch-generate follow-ups.

## Quick Start

### Draft a New Email

Provide the recipient, subject, and tone. The composer handles the rest:

```
Draft an introduction email to alice@example.com about a collaboration opportunity.
Tone: friendly, not too casual
```

The composer will:
1. Generate subject line and body
2. Show you the preview
3. Create a Gmail draft (unsent) so you review before sending

### Reply to an Existing Email

Paste the original email text or thread ID. The composer reads context and drafts a reply:

```
Reply to John's email about the project deadline. He's concerned about scope.
Tone: reassuring but realistic
```

The composer:
1. Reads the original message via Gmail MCP
2. Extracts key points (deadline, concern, expectation)
3. Drafts a contextual response
4. Creates a Gmail draft in the original thread

## Tone Patterns

Each tone has a signature style. Pick one:

### Formal
- Professional, courteous, measured
- Use: executives, first contact, legal matters, escalations
- Example: "I wanted to follow up on our conversation..."
- Avoid: contractions, exclamation marks, humor

### Friendly
- Warm, approachable, conversational
- Use: internal team, established relationships, casual requests
- Example: "Quick question â when's a good time to connect?"
- Include: light humor if appropriate, conversational openings

### Urgent
- Direct, action-oriented, time-sensitive
- Use: deadlines, blockers, immediate decisions needed
- Example: "This needs resolution by EOD today. Here's the situation:"
- Include: clear timeline, explicit asks, bold for key dates

### Apologetic
- Sincere, accountable, forward-focused
- Use: mistakes, missed deadlines, service failures
- Example: "I apologize for the delay. Here's what went wrong and how I'm fixing it."
- Include: acknowledge impact, explain root cause, concrete recovery plan

### Persuasive
- Reasoned, benefit-focused, data-backed
- Use: requests, proposals, buy-in for ideas
- Example: "This approach reduces cycle time by 40%..."
- Include: problem â solution â impact â ask

## Template Scenarios

### Follow-up After No Response

**When**: You sent something; radio silence for 7+ days.

**Composer generates**:
- Subject line: "Quick follow-up â [original topic]"
- Friendly check-in: "Wanted to circle back on..."
- One specific reminder of ask
- New deadline or next step
- Easy out: "If timing is bad, let me know"

### Soft Decline

**When**: You can't say yes but want to keep the relationship.

**Composer generates**:
- Opening: "Thanks for thinking of me"
- Clear, brief refusal (no lengthy justification)
- Why (one sentence, not a wall)
- Alternative or future possibility
- Warm close

### Meeting Request

**When**: You need a slot on someone's calendar.

**Composer generates**:
- Subject: "Request for 30-min call â [topic]"
- Context: Why now, why them
- 3â4 specific time options (no "whenever works")
- Fallback: Calendly link or Outlook invite
- Tone: respectful of their time

### Thank You

**When**: Someone helped, referred, or supported you.

**Composer generates**:
- Specific callout: What exactly they did
- Impact: How it helped
- Forward look: How you'll pay it forward or next step
- Avoid: Generic gratitude, obligation

### Escalation

**When**: Issue unresolved at current level; needs higher authority.

**Composer generates**:
- Context: What was attempted, when
- Root issue: Why the lower level didn't work
- Clear ask: What you need from them
- Respect: Acknowledges their time and complexity
- Tone: Urgent but not hostile

## Gmail Integration

### Create a Draft (No Send)

```
Draft an email asking for project resources by EOD Thursday.
Recipient: manager@company.com
Tone: urgent
Create as Gmail draft
```

The composer uses `gmail_create_draft` to save it unsent. You review, edit, and send manually from Gmail.

### Reply to a Thread

```
Reply to thread ID abc123def456.
Original message: "Can you review the Q2 plan?"
Tone: friendly
```

The composer:
1. Calls `gmail_read_thread` to fetch the full conversation
2. Identifies the most recent message
3. Drafts a contextual reply
4. Creates a Gmail draft in the same thread (In-Reply-To set automatically)

## Email Etiquette Guidelines

### Subject Lines

**Good**: Specific, scannable, action if needed
- "Q2 Roadmap Review â Feedback Needed by Fri"
- "Re: Project X â Timeline Update"

**Bad**: Vague, all-caps, or emergency-crying-wolf
- "Hi"
- "URGENT!!!"
- "FW: FW: RE: Stuff"

### Opening Moves

**Strong**: Personal, purpose-driven
- "Hi Alice â I wanted to revisit the timeline question from our call."

**Weak**: Generic or stalling
- "Hope this finds you well"
- "I hope you are doing great"

### Length & Structure

- **Rule of 3**: Open + 3 short paragraphs max + close
- **Paragraph 1**: Purpose or context
- **Paragraph 2**: Detail/ask
- **Paragraph 3**: Next step or timeline
- **Close**: Name, sign-off (no flowery closures unless very formal)

### Sign-Offs

| Tone | Sign-off |
|------|----------|
| Formal | Best regards, Sincerely |
| Friendly | Cheers, Talk soon |
| Urgent | Looking forward to your response |
| Apologetic | Thank you for your patience |

### Response Time Expectations

- **Urgent**: Expect response within 24 hours â mention a deadline
- **Time-sensitive**: 2â3 days â "by end of week" or specific date
- **Routine**: 3â5 days â no urgency signal needed

## Composing Tips

### For Requests

1. **Lead with impact**: "This change cuts deployment time by 30%"
2. **Explain the ask**: "I need sign-off from you by Thursday"
3. **Ease pushback**: "If there are blockers, let's talk"

### For Corrections or Apologies

1. **Name it**: "I dropped the ball on the deadline"
2. **Explain (briefly)**: "Scope creep on another project"
3. **Fix it**: "Here's the corrected version, delivery tomorrow"
4. **Prevent recurrence**: "I'm adding this to my process to flag early"

### For Complex Topics

- Use **bullet points** for clarity
- Keep **paragraphs short** (3â4 sentences)
- **Lead with summary**: "TL;DR: We hit our Q2 KPI by 15%"
- **Link to details**: "Full breakdown here [link]" rather than inline walls of text

## Batch Composition

Need to send similar messages to multiple recipients?

```
Generate follow-up emails for these people on the project status:
- alice@company.com
- bob@company.com
- carol@company.com

Base template: "Wanted to loop you in on where we landed with Project X..."
Tone: friendly, informative
Create as Gmail drafts
```

The composer generates one draft per recipient, personalizing details. You review each, edit as needed, and send from Gmail.

## Recipes

### Quick Feedback Reply

```
Respond to jane@company.com's draft proposal.
You agree with 80% and have 2 concerns.
Tone: constructive
```

Composer generates praise, specific concerns with reasons, and one suggestion for fix.

### Difficult Conversation Opener

```
Draft an email to schedule a 1:1 about performance.
Be direct but not scary.
Tone: respectful, clear
```

Composer avoids jargon, sets a specific time, explains it's a normal check-in.

### Vendor Inquiry

```
Ask a vendor about SLA terms, pricing for 500 users, and implementation timeline.
Tone: professional
```

Composer asks each question clearly, signals you're evaluating options (not yet committed), and requests timeline for a decision.

---

## Common Phrases by Tone

**Formal Openers**: I wanted to reach out about..., I hope this email finds you well, Per our conversation...

**Friendly Openers**: Quick question..., I wanted to loop you in on..., Thought you'd find this interesting...

**Urgent Openers**: This needs immediate attention because..., Time-sensitive: ..., Action needed by [DATE]...

**Apologetic Openers**: I apologize for..., I should have..., My mistake â here's the fix...

**Persuasive Openers**: This opportunity will..., The data shows..., I'd like to propose...

