# Question1

## What I'm building

A priority inbox that shows the top 10 most important unread notifications to the user. The idea is that not all notifications are equal - a placement drive is way more important than a sports day announcement, so it should always show up first.

---

## How I'm calculating priority

I'm giving each notification a score based on two things:

**1. Type weight**
- Placement = 3 (highest)
- Result = 2
- Event = 1 (lowest)

**2. Recency**
- Newer notifications should rank higher within the same type
- I'm using log decay so the score doesn't drop off too fast

```
score = type_weight * 10 + max(0, 10 - log(hours_ago + 1))
```

Multiplying by 10 makes sure type always dominates over recency. So a placement from yesterday will always beat an event from today.

---

## Data structure: Min Heap

I'm using a min heap of size N to keep track of the top N notifications.

The way it works:
- The smallest score always sits at the top of the heap
- When a new notification comes in, I check if its score is bigger than the top of the heap
- If yes, I replace it. If no, I ignore it.
- This keeps only the top N at all times

This is O(log N) per insert which is much better than sorting everything every time.

---

## Handling new notifications

When a new notification comes in live, I just call `add_new_notification()` which does the same heap logic. No need to re-process everything from scratch.

---

## How to connect the real API

```python
import requests
headers = {"Authorization": f"Bearer {TOKEN}"}
response = requests.get(API_URL, headers=headers)
notifications = response.json()["notifications"]
```

---

## Files

- `Stage-1.py` - main code
- `screenshots/` - terminal output screenshots
