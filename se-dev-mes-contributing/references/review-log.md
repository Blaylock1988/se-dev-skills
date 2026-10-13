# MES review log

One entry per PR: what was submitted, what the maintainer said, and the lesson. Newest first. Fold lessons into
`SKILL.md`.

## MeridiusIX/Modular-Encounters-Systems#390 Add autopilot flight rules (VillanorSE, 2026-10-12) - declined, then restructured

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

## MeridiusIX/Modular-Encounters-Systems#389 Reset static encounters (VillanorSE, 2026-10-12) - changes requested

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
- **Found while retesting:** following the suggestion literally mattered. Using `IsIdentityPlayer` for block owners
  let an owner MES couldn't place count as "not a player" (a player's ownerless plane would have been removed);
  `IsIdentityNPC`, as suggested, makes an unknown owner block the reset. Planet-direction placement over water also
  turned out to differ between world start and a reset; filed separately as MeridiusIX/Modular-Encounters-Systems#395
  (one bug per issue).

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
