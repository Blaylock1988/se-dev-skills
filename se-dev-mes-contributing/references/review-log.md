# MES review log

One entry per PR: what was submitted, what the maintainer said, and the lesson. Newest first. Fold lessons into
`SKILL.md`.

## MeridiusIX/Modular-Encounters-Systems#451 RotateInstallationsMin/Max read the wrong entry (VillanorSE, 2026-10-25) - issue, open

- **Submitted:** bug issue only (found reading code, not seen in game): the Min/Max branch in PrefabSpawner checks
  PrefabIndexes[i] but reads the lists with the loop counter i, so Random/SelectedIndexes spawning modes get another
  slot's range. Linked the working RotateInstallations branch next to it, said it's minor, offered a small PR.
- **Owner:** posted as drafted. A companion feature draft (RotateInstallations in degrees) was held back as "too
  wordy": cut it to the need, the mechanism in one line and the proposed tag.

## MeridiusIX/Modular-Encounters-Systems#408 Clean up invalid escort slots, ClearParentEscort action (Mike Dude, 2026-10-17) - merged 2026-10-18

- **Review:** enenra asked for a rename: `ClearParentEscort` matched the internal field but didn't say whether it
  runs on the escort or the leader, and "parent" means nothing to a modder. Renamed to `LeaveEscortSlot` (run on the
  escort; the leader can fill the slot again), wiki says "leader". enenra also asked whether a null check on `Escort`
  was needed; the answer linked where every behavior creates its Escort subsystem and noted the action returns early
  with no leader, so no check was added.
- **Lesson:** folded into SKILL.md section 1 (name a tag for what the modder sees and which grid runs it).
- **Lesson:** answer a "does this need a safety check" question with the line that guarantees it, not just "no".

## MeridiusIX/Modular-Encounters-Systems#363 Tags & Tokens wiki pages (Mike Dude, 2026-09-23) - open, start over

- **Review:** enenra wants the pages but rejected the text: about 300 generated lines, "way too wordy while not being
  clear enough"; asked for 90% less, human-written text, with the page structure agreed first. Terms: not "Tokens"
  (suggested "Variables"); "Tag" means any MES parameter, so call the feature the "Tagging" system and always say
  Trigger Tag / Event Tag in the singular.
- **Lesson:** folded into SKILL.md section 4 (agree structure first, keep it short, Tagging terms).

## MeridiusIX/Modular-Encounters-Systems#400 MaxWaterCoverage for every spawn group (VillanorSE, 2026-10-16) - merged 2026-10-17, released in MES 2.75.00

- **Issue:** #397, enenra: "Yes, sounds good. Please submit a PR."
- **Submitted:** the MaxWaterCoverage check moved out of the requiresWater block (still only when > 0, default -1),
  plus a first wiki entry for MaxWaterCoverage. Owner reviewed, edited the wiki wording and opened the PR themself.
- **Testing:** in game with a low test value (0.1) so a coastal spot inside a territory exceeds it; /MES.GESAP shows
  the water ratio and the eligible list, so no waiting for spawns.
- **Lesson:** to test a threshold tag where the natural trigger is hard to reach, set the threshold low enough that a
  reachable spot crosses it, and read eligibility with /MES.GESAP.

## MeridiusIX/Modular-Encounters-Systems#399 Random waypoint altitude respects water (VillanorSE, 2026-10-16) - open, waiting on an in-game test

- **Issue:** #366 (2026-09-24). enenra first wanted a way to respect or ignore water, then said on Discord that just
  respecting it is fine.
- **Submitted:** GetRandomCoords uses the water-aware PlanetEntity.SurfaceCoordsAtPosition (old terrain lookup as
  fallback), plus waypoint tag `[IgnoreWater]` (default false) passed into its existing ignoreWater parameter; wiki
  entry in Waypoint.md. A deliberate default change, agreed with the maintainer, not opt-in. Build-only, no in-game test.
- **Process slip:** "push and open it" was meant as local prep; the PR went up before the owner had reviewed the
  body and wiki text. Owner then edited both on GitHub: dropped "as agreed on Discord" for a plain reason ("the
  likely more common use case") and rewrote the wiki entry to lead with what the tag does.
- **Lesson:** confirm explicitly before opening anything public on the MES repo, even after a "push and open"; the
  owner reviews the final PR body and wiki wording first.
- **Review:** enenra: "wait with this one until you've tested it", and asked whether `[IgnoreWater]` should also
  cover waypoint placement so the two match.
- **Lesson:** a build-only PR waits; test in game before opening.
- **Lesson:** when the maintainer agrees a default change, reuse an existing parameter for the opt-out so the tag
  costs one line of logic.

## MeridiusIX/Modular-Encounters-Systems#397 Let MaxWaterCoverage apply to dry-land installations (VillanorSE, 2026-10-15) - issue posted

- **Owner's edits to the draft before posting:** opened with who it affects in general instead of "my scenario mod";
  added that it's minor and doing nothing is fine; cut a simulated "one in eight" figure; described the Small/Medium
  pool setup concretely; added a plain "result of this fix" paragraph and a usage example (MaxWaterCoverage:0.6);
  closed with "If you are open to it, I'm happy to open a small PR for this."; paragraphs not hard-wrapped.
- **Lesson:** folded into SKILL.md section 0 (general framing first, honest stakes, no unverifiable numbers, result +
  example after the fix, softer close, no hard wraps).

## GV-Server-Mods/se-dev-mes#10 Clarify when inline spawn condition tags apply (VillanorSE, 2026-10-15) - merged 2026-10-18

- **Not an MES PR:** a fix to the shared se-dev-mes skill, which takes PRs directly (no issue first).
- **Why:** the skill said condition tags written inline in a spawn group's Description "are honored". In my mod, 36
  spawn groups had `[PlanetaryInstallationType:]` next to `[SpawnConditionsProfiles:]`, and every installation
  spawned Small.
- **Submitted:** one bullet in `references/spawning_and_conditions.md`. The Description is conditions profile 0, the
  referenced profiles are appended after it rather than merged, and profile 0 without a spawn type never matches. So
  inline condition tags are ignored next to `[SpawnConditionsProfiles:]`. The bullet also covers
  `[UseFirstConditionsAsPersistent:]` and the `Small` default. Branch based on upstream `main`. Problem / What MES
  does / Change / Testing headings. Testing says docs only, checked against the Workshop MES source, no in-game test.
- **Lesson:** when a skill's wording causes a mod bug, fix the skill upstream as well as the mod, and cite the
  source lines that prove the corrected wording.

## MeridiusIX/Modular-Encounters-Systems#395 issue and #396 fix (VillanorSE, 2026-10-13) - merged 2026-10-14, released in 2.75.00

- **Issue:** short, Problem / Seen in game / Fix headings, permalinks at a full SHA, measured numbers (ports raised
  0.8-9.2 m, two resets 1 m apart = waves), the fix tied to existing code (PathPlacements already ignores water),
  ended with "Happy to open a PR for this".
- **enenra:** "Yes, a PR would be welcome for this." within the day.
- **PR #396:** one line, `Fixes #395`, tested in game before opening (re-created encounter lands at its world-start
  height).
- **Lesson:** the issue-first route works when the issue carries measured evidence and a fix that matches existing
  code.

## MeridiusIX/Modular-Encounters-Systems#390 Add autopilot flight rules (VillanorSE, 2026-10-12) - declined, restructured, merged 2026-10-15

- **Submitted:** 6 autopilot tags (MaxAngleFromVelocity, RollIntoTurns, MaxBankAngle, ReturnToLevel,
  ReturnToLevelMinSpeed, MaxRotationStep), each default off, ~150 lines inside `RotationSystem.cs`.
- **enenra:** declined: the change was too large to review for code that isn't widely known, and needs someone
  available to review and maintain it.
- **Blaylock1988 (Mike Dude):** supportive, offered to test; suggested one master tag and a separate file.
- **Restructure:** `[UseAircraftFlightModel:true]` master tag; all logic in new `AircraftFlightModel.cs` (partial
  class); 6 gated lines in `RotationSystem.cs`; new `GyroscopeProfile.UpdateRotationWorld`, `UpdateRotation`
  untouched.
- **Lesson:** big core changes need isolation (master tag + own file), in-game proof with the flag off, and a second
  reviewer/tester. Size alone is a reason to decline.
- **Revision 2 (2026-10-13):** restructured as above, rebased, explanatory code comments and co-author trailers
  removed, retested (flag off: an NPC cargo ship unchanged; flag on: fighters smooth), description replaced, reply
  posted. A declined PR has no formal review, so there is no re-request button; the reply notifies the maintainer.
- **enenra on revision 2 (2026-10-13):** the wiki entries were "pretty much indecipherable" (they explained the math:
  override targets, angular velocity, formulas), and asked whether MaxRotationMagnitude and AngleAllowedForForwardThrust
  already do the same as MaxRotationStep and MaxAngleFromVelocity. Lesson: wiki entries say what the tag does to the
  grid in play, and a new tag that sounds like an existing one states the difference. Check every claim in a reply
  against the source before posting; correcting a mistaken reply plainly is fine.

- **enenra review (2026-10-15, changes requested):** three naming comments in `AutoPilotProfile.cs`: RollIntoTurns
  and ReturnToLevel take numbers but read like true/false (suggested e.g. RollIntoTurnsRate); ReturnToLevelMinSpeed
  implies a MaxSpeed that doesn't exist (suggested ReturnToLevelCutoff/CutoffSpeed). Answer: renamed to
  RollIntoTurnsMultiplier and ReturnToLevelMultiplier (as RotationMultiplier; chosen over his Rate suggestion for
  consistency with existing tags) and ReturnToLevelSpeedCutOff (as StrafeSpeedCutOff), each reply citing the existing tag at a permalink. Lesson: a
  numeric tag's name says what the number is; reuse the suffixes MES already has (Multiplier, CutOff) and cite them.
- **Merged (2026-10-15):** after the renames, enenra approved without further comments and merged the same day
  (merge commit `fc5cb29`), as "Add optional aircraft flight model for gyro-steered planes ([UseAircraftFlightModel])".
  Lesson: a large central change that was declined can still merge once it's isolated behind a master tag in its
  own file, a second modder has tested it, and every review point is answered. A merge is not a Workshop release:
  don't build on the tags until a release ships them.

## MeridiusIX/Modular-Encounters-Systems#389 Reset static encounters (VillanorSE, 2026-10-12) - merged 2026-10-14, released in 2.75.00

- **Submitted:** ResetThisStaticEncounter fix + `/MES.Debug.RefreshStaticEncounters` (dry run, `.Go`, `.Go.<group>`)
  + a mod API to trigger it.
- **enenra's comments:**
  - note should read "Successful Reset: ...";
  - the API addition "has no relation to the PR and is not used" -> removed;
  - wiki: "re-arm" unclear; no programming terms like "closing" a grid or "watchers";
  - command naming: use "reset" like the existing command; `/MES.Debug.ResetStaticEncounters` does it,
    `.Info` for the report; no "Go"; the per-group form duplicates `ResetThisStaticEncounters.<group>`;
  - "armed" terminology in player-facing text;
  - use FactionHelper's identity functions ("Not NPC does not mean it's a player");
  - the task class belongs in its own file in `Tasks/`.
- **Lesson:** no extras beyond the purpose; follow existing command verbs and `.Info`; plain words in all text; use
  MES helpers; one class per file in the right folder.
- **Revision 2 (2026-10-13):** all points addressed; replied on every thread (including a closing "Done" on the ones
  first answered with "I'll update"); re-requested review; replaced the description, which still described revision 1.
- **Found while retesting:** placement over water differs between world start and a reset; filed separately as
  MeridiusIX/Modular-Encounters-Systems#395 (one bug per issue).
- **enenra on revision 2 (2026-10-13):** the owner check must use `IsIdentityPlayer`, not the negation of
  `IsIdentityNPC` ("checking for player owned by negating NPC owned again. That is not correct"), and `IsBot` should
  never be used ("not accurate"); use the negation of `IsIdentityPlayer`. Revision 2 had switched to `!IsIdentityNPC`
  after a test plane looked like an NPC, but that plane was ownerless; `IsIdentityPlayer` was correct all along.
  Lesson: rule out the test setup (ownership, who is where) before changing code that the reviewer asked for.

## MeridiusIX/Modular-Encounters-Systems#377 Default MaxWaterDepth to -1 (VillanorSE, 2026-09-27) - merged

- **Submitted:** +15/-5. Problem section with file/line evidence of the behavior change from 48869a4.
- **enenra:** one logic issue, fixed with a suggested change (`useMaxDepth` guard); then approved.
- **Lesson:** small, evidence-led fixes to a recent regression merge quickly. Check boundary logic carefully; accept
  suggested changes.

## Field notes from Blaylock1988 (Mike Dude), 2026-10-12

- Drawn from his issues and PRs (MeridiusIX/Modular-Encounters-Systems MeridiusIX/Modular-Encounters-Systems#380, MeridiusIX/Modular-Encounters-Systems#384, MeridiusIX/Modular-Encounters-Systems#386, MeridiusIX/Modular-Encounters-Systems#388, MeridiusIX/Modular-Encounters-Systems#392, MeridiusIX/Modular-Encounters-Systems#393) and the MeridiusIX/Modular-Encounters-Systems#390
  review. Folded into SKILL.md sections 0, 1, 2, 5, 6 and 8: verify on current master, one bug per issue, permalinks at
  a SHA, concrete fixes reusing MES patterns, smallest diff, no new code comments, no AI tells or co-author trailers,
  qualified cross-repo references, document released MES only.
- Short headings in issues and PRs are kept (VillanorSE preference, 2026-10-12).

## Other contributors (patterns seen)

- MeridiusIX/Modular-Encounters-Systems#358 (jayph, zone admin commands): enenra asked to keep dot separators instead of spaces, to move a method for
  consistent ordering, and checked that a wiki line had code behind it. Approved after changes and an in-game test.
- MeridiusIX/Modular-Encounters-Systems#386 (Blaylock1988, +149): `Fixes #384`, explains the root cause, adds tags the issue asked for plus two more with a
  concrete reason ("I need these for my convoys"), includes the wiki file. Approved without comments.
