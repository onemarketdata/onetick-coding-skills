# onetick-coding-skills

A Claude Code **plugin marketplace** delivering OneTick coding skills. Currently ships one plugin:

| Plugin | What it does |
|---|---|
| **onetick-py-coding** | Write, debug, and review `onetick.py` (`otp`) code against the OneTick tick database. Bundles the full onetick-py API reference (every function/class/method as a lookup-able markdown file), curated examples, and the coding rules that prevent common mistakes. |

## Install (end users)

In Claude Code, add the marketplace then install the plugin. **Use a full git URL ending in `.git`**
(the bare `owner/repo` shorthand is GitHub-only, and a URL without `.git` is treated as a direct
manifest link and fails):

```
# SSH (recommended)
/plugin marketplace add git@github.com:onemarketdata/onetick-coding-skills.git
/plugin install onetick-py-coding@onetick-coding-skills
```

HTTPS works too (note the trailing `.git`; append `#master` to pin the branch):

```
/plugin marketplace add https://github.com/onemarketdata/onetick-coding-skills.git
/plugin install onetick-py-coding@onetick-coding-skills
```

Or from a local clone (most reliable — no network/auth dependency):

```
git clone git@github.com:onemarketdata/onetick-coding-skills.git
/plugin marketplace add ./onetick-coding-skills
/plugin install onetick-py-coding@onetick-coding-skills
```

After install, the `onetick-py-coding` skill triggers automatically whenever you ask Claude to write
or fix OneTick / `onetick.py` code. Verify it's loaded with `/plugin` (Manage plugins) or by asking
an otp question and watching it consult the bundled reference.

> Gotchas: the bare `owner/repo` form only works for GitHub; for self-hosted GitLab use the full
> git URL **with the `.git` suffix** (without it, Claude Code treats the URL as a direct
> `marketplace.json` link — the cause of *"Invalid marketplace schema from URL"*).

## What's inside

```
onetick-coding-skills/                         (this repo = the marketplace)
├── .claude-plugin/
│   └── marketplace.json                        # marketplace manifest (lists plugins)
└── plugins/
    └── onetick-py-coding/                       (a plugin)
        ├── .claude-plugin/
        │   └── plugin.json                      # plugin manifest
        └── skills/
            └── onetick-py-coding/               (the skill)
                ├── SKILL.md                     # entry point: workflow + when to use
                ├── coding-rules.md              # distilled otp coding rules ("avoid common mistakes")
                ├── INDEX.md                     # index of all 548 reference files (title + purpose)
                ├── reference/
                │   ├── docs/                     # onetick-py API reference + guides (auto-generated)
                │   └── examples/                 # curated, hand-written onetick.py examples
                └── scripts/
                    ├── build_index.py            # regenerates INDEX.md
                    └── sync.sh                   # maintainer: refresh reference from upstream
```

To add more OneTick skills later, drop another plugin under `plugins/<name>/` and add an entry to
`.claude-plugin/marketplace.json`.
