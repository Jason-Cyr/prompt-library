# End-of-Day Reflection Workflow

A prompt your agent sends at the end of the workday to help you reflect and close out the day. Works well as a Slack DM or your preferred messaging surface.

## The Prompt

```
It's end of day. Send a brief reflection prompt to help close out the day.

Review today's context:
- What was on the calendar today?
- What tasks were completed or progressed?
- What's still open?

Then ask 2-3 short reflection questions, like:
- What's the one thing that moved the needle today?
- Anything you want to carry to tomorrow?
- Anything on your mind you want to capture before logging off?

Keep it conversational and brief. This should feel like a quick check-in,
not a performance review.
```

## Setup (OpenClaw Cron)

```yaml
{
  "label": "eod-reflection",
  "schedule": "0 17 * * 1-5",     # 5:00 PM weekdays
  "prompt": "It's end of day...",
  "channel": "slack",
  "model": "haiku"
}
```

## Why It Works

- Forces a pause before context-switching to personal time
- Captures thoughts while they're fresh (agent writes responses to daily notes)
- Surfaces carry-over items that might otherwise get lost overnight
- Builds a journaling habit without the friction of opening a blank page
