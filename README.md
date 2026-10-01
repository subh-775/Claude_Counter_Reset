# Claude_Counter_Reset

GitHub Actions workflow that keeps a Claude 5-hour usage window always
running, by sending "hi" (Haiku) right after each window resets.

## How it works
- Each ping runs `claude -p` with `--output-format stream-json`, which reports
  the exact reset time of the current 5-hour window. `next_run.txt` is set to
  that time + 30 s and committed.
- The ping then starts the next run of the workflow (a "waiter") that sleeps
  until that time, pings, and starts the next waiter. One waiter at a time.
- A cron every 10 min is only a backup: if a ping is over 5 min overdue, it
  pings and restarts the chain.

## Setup
1. `claude setup-token`, approve in the browser, copy the `sk-ant-oat01-...` token.
2. `gh secret set CLAUDE_CODE_OAUTH_TOKEN` and paste it.
3. `gh workflow run claude-warmup` to ping now and start the chain.

## Check on it
`gh run list --workflow warmup.yml --limit 10`, or the "next ping: ..." commits.

## Notes
- Keep the repo public: a waiter sleeps ~5 h, free only on public repos.
- A waiter shows "in progress" for ~5 h; that's normal.
- The token expires after ~1 year. On a "Claude token rejected" error, repeat
  Setup steps 1-2.
