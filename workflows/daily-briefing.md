# Daily Briefing Workflow

A morning briefing your agent delivers before you start your day. Checks calendar, tasks, weather, and carries over unfinished items from yesterday.

## The Prompt

```
Generate my morning briefing. Check:

1. Calendar — What's on my schedule today and tomorrow?
2. Tasks — What's due or overdue? What's in progress?
3. Weather — What's the forecast for today?
4. Yesterday's carry-over — Read yesterday's daily note. What didn't get done?
5. Anything notable — Unread messages, upcoming deadlines, things I should know about.

Format: concise bullet points grouped by category. Lead with the most important thing.
No fluff. If nothing's notable, say so — don't pad the briefing.
```

## Setup (OpenClaw Cron)

Schedule this to run before your wake time:

```yaml
# In openclaw.json cron config
{
  "label": "morning-briefing",
  "schedule": "0 5 * * *",        # 5:00 AM daily — adjust to your wake time
  "prompt": "Generate my morning briefing...",
  "channel": "slack",              # or telegram, discord, etc.
  "model": "haiku"                 # fast/cheap model is fine for this
}
```

## Tips

- Run it 30-60 minutes before you typically wake up so it's waiting for you
- Use a cheaper/faster model — this doesn't need heavy reasoning
- The carry-over check requires the agent to maintain daily notes in `memory/YYYY-MM-DD.md`
