# DevPulse

A small command line developer brief. Each run it pulls the top of Hacker News and the
repositories trending on GitHub, then has an AI model tell you what the field is paying
attention to today. It prints on screen. With no model key set it still prints a clean
plain list.

All data sources are free and need no key. The only key you add is a free Gemini key
for the write up.

## Sources

| Signal | Source | Key |
|---|---|---|
| Top stories | Hacker News API (hacker-news.firebaseio.com) | none |
| Trending repositories | GitHub search API (api.github.com) | none |
| Brief write up | Google Gemini (free tier), Claude optional | free |

GitHub's public search allows a small number of unauthenticated requests per minute,
which is plenty for one brief a day.

## Setup

```
cd devpulse
py -3 -m pip install -r requirements.txt
py -3 -m devpulse init       # creates config.toml
notepad config.toml          # paste your free Gemini key under [keys]
```

Get a free Gemini key at https://aistudio.google.com/apikey.

## Run it

```
py -3 -m devpulse brief      # pull the news and write the brief
py -3 -m devpulse check      # test each source and show which work
py -3 -m devpulse init       # create config.toml
```

## Everyday skills

On top of its own speciality, DevPulse does the day to day things a real assistant does.
All of these are free. The weather and lookup are keyless; answering uses your Gemini key.

```
py -3 -m devpulse ask "how far is the moon"     # ask it anything, answered honestly
py -3 -m devpulse weather "London"               # current conditions and today's range
py -3 -m devpulse remember "call the bank"      # save a quick note
py -3 -m devpulse notes                         # show your saved notes
py -3 -m devpulse forget                        # clear your notes
```

`ask` pulls a quick reference before answering and is told to say when it is unsure
rather than guess. `weather` works for any place name. Notes are kept in a small local
`notes.json` next to the tool, which git ignores.

## Learns as you go

DevPulse grows on you. Each run it keeps a small local `profile.json` next to the tool
(git ignored, yours alone, on your machine only) and shapes itself around how you use it.

```
py -3 -m devpulse profile      # see what it has learned about you
py -3 -m devpulse reset        # wipe everything it has learned
```

It notices the topics you keep asking about and leans the write up gently that way, while
still reporting only real data. It remembers the places you check weather for, so `weather`
with no place named uses your usual one.

## How it is built

```
devpulse/
  core.py      config, web requests, the model write up, logging
  sources.py   Hacker News and GitHub trending (each isolated)
  abilities.py the shared everyday skills (ask, weather, notes)
  learn.py     the learning layer: the growing local profile
  brain.py     DevPulse's identity, the prompt, and the no-model fallback
  cli.py       the init / brief / check commands
```

Design notes:

- **Graceful.** If one source is down the brief still prints with the rest.
- **Model fallback.** Gemini model, then a backup if it is overloaded, then Claude if a key
  is set, then a plain list. Something always prints.
- **Honest.** The model is told to use only the stories and repositories in the data, never to
  invent one, and never to inflate a headline beyond what it says.
- **No secrets in the repo.** Keys live only in `config.toml`, which git ignores.

## Licence

Released for personal and educational use.
