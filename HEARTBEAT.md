# HEARTBEAT.md - Proactive Checks

## Heartbeat Schedule
Two checks per day (monitoring token usage):
- **Morning (8 AM Denver):** Daily summary review + poseidons_todo check
- **Evening (5 PM Denver):** MissionControl status + poseidons_todo check

## Morning Check (8 AM)
1. Review yesterday's daily summary from daily-summaries.txt
2. Extract any significant work, blockers, or insights
3. Check poseidons_todo.txt for any items to address
4. Brief context setting for the day

## Evening Check (5 PM)
1. Light MissionControl status check:
   - Any uncommitted git changes?
   - Database updated recently?
   - Any obvious issues in project structure?
2. Check poseidons_todo.txt for pending items
3. Flag anything that needs attention

## What Triggers Alerts
Only surface something if:
- There are uncommitted changes worth discussing
- poseidons_todo.txt has items that need action
- Something in daily summary needs follow-up

Otherwise: brief HEARTBEAT_OK or silent acknowledgment
