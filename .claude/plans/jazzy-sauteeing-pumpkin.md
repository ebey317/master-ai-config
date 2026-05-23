# Plan: Open Kimmy AI and Review Page

## Context
The sensei browser tab got stuck in an error state after navigating to `kimmyai.com` (bad URL). All subsequent browse/read/search calls return `"Frame with ID 0 is showing error page"`. Need to reset the tab and navigate to the correct Kimmy AI URL, then do a full top-to-bottom page review.

## Problem
1. Correct URL for "Kimmy AI" is unknown — `kimmyai.com` 404'd
2. Tab is stuck in error state, blocking all further sensei operations

## Implementation Steps

1. **Reset the stuck tab** — browse to `https://google.com` (known-good) to clear the error frame
2. **Find correct Kimmy AI URL** — use sensei search for "Kimmy AI" once tab is healthy
3. **Navigate to Kimmy AI** — browse to the resolved URL
4. **Read and review** — call sensei read and report full top-to-bottom content review to user

## Verification
- `sensei read` returns a valid page_context (no error frame) after reset
- Final page title/URL confirms we're on Kimmy AI, not a redirect
