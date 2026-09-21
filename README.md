# cuebids-status

One file, `status.json`, that the Cuebids web app reads on boot and whenever it
returns to the foreground. It is how we tell players about an outage or planned
maintenance without depending on anything that might be the thing that is down.

Editing `status.json` on `main` publishes to every player. GitHub's CDN caches
for about five minutes, so allow that long for a change to show or clear.

## No notice

```json
{}
```

## A notice

```json
{
  "id": "2026-09-21-firestore",
  "level": "outage",
  "title": { "en": "It's not you", "sv": "Det är inte du" },
  "message": {
    "en": "Google's database service is having trouble, so pages may not load. Your games are safe.",
    "sv": "Googles databastjänst har problem, så sidor kan vägra ladda. Dina spel är säkra."
  },
  "link": "https://discord.gg/...",
  "until": "2026-09-22T00:00:00Z"
}
```

| field | |
|---|---|
| `id` | required. A new id shows the notice again to players who dismissed the last one. |
| `level` | required. `info`, `maintenance` or `outage`. |
| `message` | required. `en` is required; other languages (`sv`, `pl`, `fr`, `es`, `zh`, `zht`) fall back to it. 400 characters at most. |
| `title` | optional, same shape as `message`. Used on the "failed to load" screen. |
| `link` | optional, `https://` only. |
| `until` | optional ISO time. The notice disappears by itself after it - set one, so a forgotten notice cannot outlive its outage. |

Anything the app cannot read - malformed JSON, an unknown level, a missing
English message, a past `until` - shows nothing. A mistake here cannot break
the app; it can only fail to show.

The reader is `apps/cuebids/src/util/serviceStatus.js` in the Cuebids repo
(CUE-419).
