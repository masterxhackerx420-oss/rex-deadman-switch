<p align="center">
  <a href="https://hits.sh/github.com/masterxhackerx420-oss/rex-deadman-switch/">
    <img src="https://hits.sh/github.com/masterxhackerx420-oss/rex-deadman-switch.svg?style=for-the-badge&label=Visitors&color=8b5cf6&logo=eye" alt="Visitors" />
  </a>
</p>

# Rex's Deadman Switch

![A hooded skull with a cigar, a dead man's lever, a dark terminal, and a sealed envelope on a coal desk.](switch.jpg)

A cron script. Not a hosted service. Standard library only (Python 3.10+).

Check in with `deadman ping`. If you miss N days, `deadman run` emails the handoff notes in your config to the contacts you chose. It mails once per silence. A later ping sends a false alarm and arms it again.

## Setup

```bash
chmod +x deadman.py
./deadman.py init
```

Edit `~/.config/deadman/config.json`: `days`, `contacts`, `notes`, and `smtp`. The directory is mode `0700` and the files are mode `0600`. Override the location with `DEADMAN_HOME`.

Then arm it:

```bash
./deadman.py ping
./deadman.py status
```

## Commands

| Command | What it does |
| --- | --- |
| `init` | Write a config template. Refuses to overwrite. |
| `ping` | Check in. If it already fired, mail a false alarm and re-arm. |
| `status` | Days silent, days left, whether it has fired. |
| `run` | Cron entry. Mails once if the window lapsed, then holds. |
| `run --dry-run` | Print the mail. Send nothing. Mark nothing. |
| `test` | Send the handoff now, subject prefixed with `[test]`. |

## Cron

```cron
0 8 * * * DEADMAN_HOME=$HOME/.config/deadman python3 $HOME/bin/deadman.py run
```

## Rules

- Never pinged: `unarmed`. Cron does nothing.
- Inside the window: `wait`.
- Past the window, not yet mailed for this silence: one email, then `hold`.
- Ping after a firing: false-alarm mail, armed again.

The notes go out in cleartext. Point at the vault. Don't put the vault in the mail.
