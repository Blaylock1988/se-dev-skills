---
name: se-dev-mes-contributing
description: Use this skill when preparing an issue, pull request, commit message or WebWiki entry for Modular Encounters Systems (MeridiusIX/Modular-Encounters-Systems), or when answering an MES maintainer's review. Covers what the maintainer (enenra) accepts, MES code and command conventions, how to write issues, PRs and wiki entries, and tone.
---

# Contributing to Modular Encounters Systems

How to get changes into MES upstream. Built from real issues and reviews; `references/review-log.md` records each one.
**Add every new review outcome to the log and fold any new lesson into this file.**

Repo: `MeridiusIX/Modular-Encounters-Systems`, branch `master`. enenra reviews and merges. The maintainer checks
claims against the code, so every post should read like a modder wrote it after reading the code. A merge is not a
Workshop release.

## 0. Issue first (MES only)

MES prefers agreeing the approach in an issue before code arrives. Other repos (e.g. the `se-dev-mes` skill,
`GV-Server-Mods/se-dev-mes`) take PRs directly.

### Before posting
- Re-check the bug on the latest upstream `master` right before posting, and cite that commit. Confirm an older report
  is still real before commenting on it.
- Trace every code path that could cause the symptom against what was seen in game, including timing. Drop paths that
  can't produce it. Never present a guess as the cause; if two causes are possible, say so or verify first.
- One bug per issue. A second, related bug gets its own issue.
- Draft first. The person posting reviews the draft before anything goes up.

### Writing the issue
- Short: what is broken, where (file and line), what was seen, the fix.
- Simple headings when they help ("Problem", "Seen in game", "Fix"); short, clear wording under each. A one-paragraph
  bug doesn't need them.
- Brief reasoning: a sentence or two on why the fix is right or safe ("other mods are unaffected because the old path
  still runs when the tag is absent"). Not an essay.
- Plain language. Keep tag and method names out of code formatting in the prose where you can.
- Link the code, don't paste it: a permalink at a full commit SHA (`.../blob/<sha>/path/File.cs#L47`) on its own line,
  so GitHub renders the snippet.
- Realistic example values (an extreme number pulls attention to the number instead of the problem).
- A concrete fix: say exactly what to change. A simpler fallback option is fine.
- Tie the fix to code that already works, and link the working code next to the broken code. It shows the fix is
  consistent with MES and is easy to verify:
  - a sibling path already does it right ("the hover thrust path already uses the forward component of velocity; use
    the same in the direct thrust path", MeridiusIX/Modular-Encounters-Systems#392);
  - similar checks behave differently ("the BehaviorTrigger checks only read their flag; the four target checks also
    clear it, so only the first trigger sees the event"): the inconsistency is the evidence.
- Say when it started if git history shows it ("added in 2.1.22").
- For features: the need, one or two possible approaches, and which questions the maintainer should decide (naming,
  where the code lives, a master tag).
- End with "Happy to open a PR for this." Then wait for the maintainer to agree.

## 1. Scope: what gets accepted

- Small, obviously correct fixes merge fast. Most merged PRs are 1-70 lines.
- Smallest diff that fixes it. No drive-by refactors, renames or style changes.
- Follow the surrounding code's patterns (naming, tag parsing, structure) so the change looks like it belongs; reuse an
  existing method instead of adding a new one where possible.
- Every line serves the stated purpose. Anything "useful later" (an API hook, an unused option, a helper for your own
  mod) is unrelated and comes out (MeridiusIX/Modular-Encounters-Systems#389).
- New behavior is opt-in: new tags whose defaults leave the existing code path untouched, so grids without the tags run
  exactly the old code. Defaults that only take effect once the feature is switched on are fine; say so.
- Large changes to central systems (autopilot, rotation, spawning) need a reviewer who knows the code and someone
  available to maintain it (MeridiusIX/Modular-Encounters-Systems#390). To make one reviewable: one master tag that defaults off; all new logic in its own
  file (a `partial class`); existing methods untouched; the PR lists exactly which existing lines change; in-game
  evidence with the flag off (ideally a dedicated server) and before/after with it on; a second modder tests it.
- Don't duplicate an existing command or tag; search `ChatMessage.cs` and the wiki first.
- A bug fix found while building a feature goes in its own commit or issue.

## 2. Code conventions

- C# 6, SE mod whitelist. Build and run the whitelist check before pushing.
- Match surrounding style: tabs, braces on the same line, blank line after `{` and before `}` in methods, `this.` as the
  file uses it. Keep method order consistent with the file.
- No new code comments, unless continuing a pattern the file already has (e.g. the `//TagName` line above each tag
  parse). Put the reasoning in the PR description.
- Use MES's own helpers: `FactionHelper` for player/NPC identity ("not NPC" is not "player"; check names at HEAD);
  `TagParse.*Check` in the profile's `InitTags`.
- Long-running work goes in its own class in `Tasks/`.
- No per-update allocations in autopilot/behavior code.

## 3. Chat commands

- Dots separate everything: `/MES.Debug.Thing.Value`.
- Follow existing verbs: next to `ResetThisStaticEncounters`, the new one is `ResetStaticEncounters`.
- A report variant is `.Info` on the action command (`/MES.Debug.ResetStaticEncounters.Info`). The `/MES.Info.`
  family holds only `Get...` state reports. No `.Go`.
- Player-visible messages say what happened in plain words: "Successful Reset: <group>".

## 4. WebWiki entries (`WebWiki/docs/*.md`)

Audience: encounter modders and server admins.

- Only document what the PR's code implements; the maintainer checks wiki lines against the code.
- No programming or insider terms ("closing" a grid, "watchers", "re-arm", "ramp frames", "API"). Say what the player
  or modder sees.
- Behavior and default in a few sentences; what the tag is for and not for; one example value if it helps.
- When one entry covers several commands or values, tie every consequence to the one that causes it ("When an old grid
  is removed using chat command 1, ..."). A sentence with no subject after a report-only variant reads as if that
  variant does it too (MeridiusIX/Modular-Encounters-Systems#389 wiki wording).
- Edit only `WebWiki/docs` (not `WebWiki/site` or `patch_notes.md`). Keep the file's CRLF line endings.
- Copy the format of a neighbouring entry (the `Tag:` cell is padded with `&nbsp;` to the same width):

```
<!--TagName-->
|Tag:&nbsp;...&nbsp;|TagName|
|:----|:----|
|Tag Format:|`[TagName:Value]`|
|Description:|What it does, in modder terms. Default value is `x`.|
|Allowed Values:|`true`<br>`false` / Any Number Greater or Equal to `0` / ...|
|Multiple Tag Allowed:|No|
```

```
|Setting:|Readable Name|
|:----|:----|
|XML:|`N/A`|
|Chat Command:|`/MES.Debug.Command`|
|Description:|What it does and when to use it.|
|Allowed Value:|...|
```

## 5. PR text and commits

- Title: what changes, in MES terms (`Fix X for Y`, `Add Z`).
- Description: `Fixes #n`, then short headed sections (e.g. Problem, Change, Testing) with a few clear lines each:
  what changed and why, and how it was tested in game (say what wasn't: dedicated server, multiplayer).
- Credit testers and whoever suggested the approach.
- Before pushing: `git fetch upstream` and rebase onto current `master`. Work from a fork (`gh repo sync`), one branch
  per PR from the fork's `master`.

## 6. Tone and voice (issues, PRs, replies, commit messages)

- First person singular: "I", "my scenario mod". Never "we"/"our".
- No AI tells: no "Generated with" footers or `Co-Authored-By` trailers, no emoji, no bold-label bullets ("Gotcha:"),
  no parenthetical size hints ("(~20 lines)"), no polished filler.
- Keep the poster's own wording when editing their draft; fix facts and typos, don't rewrite.
- Frame claims as findings ("I looked at X and found Y"). Don't volunteer statements about AI use.
- Check every factual claim against the source or the wiki before it goes in.
- Outside the MES repo, qualify references: `MeridiusIX/Modular-Encounters-Systems#392`, never a bare `#392`.

## 7. Answering review

- Reply to every line comment briefly: what changed, or why not. Agree when the point is right.
- Push as new commits or a clean rewrite (`--force-with-lease`) and say which.
- Close every thread you answered with "I'll update" once it's done, replace the PR description if it describes
  an older version, and re-request review (the circular arrow next to the reviewer).
- Retest in game after changes and update the testing line.
- If the maintainer declines, ask what would make it acceptable before reworking.

## 8. After posting

- Don't build your own content on a tag or behavior until it's merged and released upstream.
- Docs, skills and tag lists describe released MES only, never an open PR or fork branch. Remove workarounds only
  after the fix ships.
- Keep the draft (title, body, replies) in a notes file named with the PR number once it exists:
  `mes-pr-<number>-<topic>.md`, first line `# MES PR #<number>: <topic>`.

## References

- `references/review-log.md`: every PR and issue, the review, and the lesson.
