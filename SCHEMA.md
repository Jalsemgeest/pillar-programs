# Pillar Program JSON Schema

This document describes every field in the Pillar program JSON format. Programs authored to this schema can be imported into the Pillar iOS app via the Programs tab → Import, or hosted at a URL and fetched directly.

> **Keeping this in sync:** whenever a field is added or changed in `ProgramPayload.swift` in the Pillar iOS app, update this document and the `validate_programs.py` script's allowed-value constants in the same commit.

---

## Top-level wrapper

Every file must begin with this envelope. A file may contain multiple programs, though one per file is the convention.

```json
{
  "schemaVersion": "1.0",
  "programs": [ ...ProgramPayload ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `schemaVersion` | string | ✅ | Always `"1.0"`. |
| `programs` | array | ✅ | One or more [Program](#program) objects. |

---

## Program

```json
{
  "id": "four_pillar",
  "name": "Four Pillar Training Program",
  "version": "1.15.0",
  "baseId": null,
  "upgradeFromVersion": null,
  "description": "...",
  "tags": ["home", "dumbbell"],
  "targetProfile": "intermediate",
  "createdAt": "2024-01-01",
  "sourceUrl": "https://jalsemgeest.github.io/pillar-programs/four_pillar_v1.json",
  "releaseNotes": "v1.15.0: added goal text to all plans.",
  "plans": [ ...Plan ],
  "exercises": [ ...Exercise ],
  "mobilityRoutines": [ ...MobilityRoutine ],
  "benchmarks": [ ...Benchmark ],
  "citations": [ ...Citation ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | ✅ | Stable identifier. Never change after publishing — the app uses it to match bundled upgrades to stored copies. Use `snake_case`. |
| `name` | string | ✅ | Display name shown in the Programs list. |
| `version` | string | ✅ | Semantic version `"MAJOR.MINOR.PATCH"` (e.g. `"1.3.0"`). The app auto-upgrades a stored program when the bundled version is newer. Increment MINOR for content changes, PATCH for corrections. |
| `baseId` | string | — | If this program is a fork of another, the original program's `id`. Informational only. |
| `upgradeFromVersion` | string | — | Minimum version of the base program this upgrade applies to. Informational only. |
| `description` | string | ✅ | Full description shown on the program detail screen. Markdown is not rendered; keep it plain text. |
| `tags` | string[] | ✅ | Free-form tags for filtering (e.g. `"home"`, `"dumbbell"`, `"barbell"`, `"time-efficient"`). |
| `targetProfile` | string | ✅ | Intended audience (e.g. `"beginner"`, `"intermediate"`, `"advanced"`). Informational only. |
| `createdAt` | string | ✅ | ISO 8601 date the program was first authored (`"YYYY-MM-DD"`). |
| `sourceUrl` | string | — | Canonical URL where the app can check for updates. When present, the "Check for update" flow uses this URL instead of a hardcoded fallback. |
| `releaseNotes` | string | — | Human-readable change summary for the current version. |
| `plans` | Plan[] | ✅ | One or more [Plan](#plan) objects. |
| `exercises` | Exercise[] | ✅ | Program-specific exercise definitions (coaching notes, citations). May be `[]`. See [Exercise](#exercise). |
| `mobilityRoutines` | MobilityRoutine[] | ✅ | Daily habits / warm-up / cool-down routines. May be `[]`. See [MobilityRoutine](#mobilityroutine). |
| `benchmarks` | Benchmark[] | ✅ | Performance benchmarks tracked on the Home screen. May be `[]`. See [Benchmark](#benchmark). |
| `citations` | Citation[] | — | Research citations referenced by exercises or the program description. See [Citation](#citation). |

---

## Plan

A plan is one variant of the program (e.g. "4-Day", "3-Day", "Home"). Each plan has its own set of workouts. A user selects one active plan at a time.

```json
{
  "id": "four_day",
  "name": "4-Day Plan",
  "scheduleDescription": "Mon · Wed · Thu · Sat",
  "daysPerWeek": 4,
  "orderIndex": 0,
  "phaseName": "Hypertrophy",
  "phaseOrder": 2,
  "phaseDurationWeeks": 10,
  "deloadWeekNumber": 10,
  "dynamicEquipment": false,
  "goal": "Build muscle mass with progressive overload...",
  "workouts": [ ...Workout ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | ✅ | Stable identifier within this program. |
| `name` | string | ✅ | Display name (e.g. `"4-Day Plan"`, `"Foundation"`). |
| `scheduleDescription` | string | ✅ | Human-readable schedule hint shown on the plan card (e.g. `"Mon · Wed · Thu · Sat"`). |
| `daysPerWeek` | int | ✅ | Number of training days per week. |
| `orderIndex` | int | ✅ | 0-based sort position within the program's plan list. |
| `phaseName` | string | — | Periodization phase name (e.g. `"Foundation"`, `"Hypertrophy"`, `"Strength"`, `"Conditioning"`). Omit for non-phased programs. |
| `phaseOrder` | int | — | 1-based position of this phase within the program sequence. Omit for non-phased programs. |
| `phaseDurationWeeks` | int | — | Intended length of this phase in weeks. Drives the phase progress bar on the Home screen. |
| `deloadWeekNumber` | int | — | Which week (1-based) within this plan is the deload week. The app suppresses phase-themed mobility routines during deload and shows an amber "DELOAD" badge on the phase tracker. Omit if this plan has no deload week. |
| `dynamicEquipment` | bool | — | When `true`, the user is prompted to select their available equipment at workout start and exercises are auto-swapped to compatible alternatives. Default: `false`. |
| `goal` | string | — | Short description of the plan's training intent. Shown in the "Program Info" sheet accessible from the three-dot menu during an active workout. |
| `workouts` | Workout[] | ✅ | Ordered list of [Workout](#workout) sessions in this plan. |

---

## Workout

A single training session within a plan.

```json
{
  "id": "four_day_mon",
  "dayLabel": "Monday",
  "sessionType": "Lower Body",
  "durationRange": "50–65 min",
  "location": "Home",
  "keyMetrics": ["Trap bar deadlift weight", "Hip thrust weight"],
  "orderIndex": 0,
  "activityType": "strength",
  "preMobilityIds": ["morning_cars"],
  "postMobilityIds": [],
  "groups": [ ...WorkoutGroup ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | ✅ | Stable identifier within this plan. |
| `dayLabel` | string | ✅ | Suggested training day (e.g. `"Monday"`, `"Day A"`). The user can override this in the schedule settings. |
| `sessionType` | string | ✅ | Session name displayed during the workout (e.g. `"Lower Body"`, `"Hinge + Horizontal"`). |
| `durationRange` | string | ✅ | Estimated duration range (e.g. `"50–65 min"`). Shown on the workout card. |
| `location` | string | ✅ | Where the workout takes place (e.g. `"Home"`, `"Gym"`, `"Outdoors"`). Informational. |
| `keyMetrics` | string[] | ✅ | Exercises the user should track as primary progress indicators. Shown as a subtitle on the workout card. May be `[]`. |
| `orderIndex` | int | ✅ | 0-based sort position within the plan. |
| `activityType` | string | — | Maps to an Apple Health workout type. Allowed values: `"strength"`, `"functionalStrength"`, `"cardio"`, `"hiit"`, `"climbing"`, `"core"`, `"mixed"`. Omit to let the app infer from exercise categories. |
| `preMobilityIds` | string[] | — | IDs of [MobilityRoutine](#mobilityroutine) objects shown as a checklist before the first exercise. |
| `postMobilityIds` | string[] | — | IDs of [MobilityRoutine](#mobilityroutine) objects shown as a checklist after the last exercise. |
| `groups` | WorkoutGroup[] | ✅ | Ordered list of [WorkoutGroup](#workoutgroup) blocks. Also accepted as `"blocks"` for legacy compatibility. |

---

## WorkoutGroup

A named block of exercises within a workout. Groups appear as section headers in the workout flow. Use groups to communicate intent (e.g. `"SUPERSET A"`, `"CORE FINISHER"`).

```json
{
  "name": "HINGE — PRIMARY",
  "orderIndex": 0,
  "exercises": [ ...PrescribedExercise ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Display name for this block. |
| `orderIndex` | int | ✅ | 0-based sort position within the workout. |
| `exercises` | PrescribedExercise[] | ✅ | Ordered list of [PrescribedExercise](#prescribedexercise) entries. |

---

## PrescribedExercise

A single exercise prescription within a workout group.

```json
{
  "exerciseId": "db_rdl",
  "sets": 3,
  "repsMin": 5,
  "repsMax": 6,
  "repsText": null,
  "load": "Heavy — RPE 8 by set 3",
  "tempo": "30X0",
  "notes": "Push hips back, feel hamstring stretch.",
  "priority": "core",
  "orderIndex": 0,
  "logMode": "reps",
  "durationSeconds": null,
  "perSide": false,
  "restSeconds": 90,
  "showHeartRateZones": false,
  "targetZoneLow": null,
  "targetZoneHigh": null,
  "timerStartMode": "immediate",
  "alternatives": [ ...ExerciseAlternative ],
  "citationIds": ["schoenfeld_2017"]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `exerciseId` | string | ✅ | Must match an `id` in the app's `ExerciseDatabase.json` or in this program's `exercises` array. **Never change after a version is published** — the app uses this to link logged history to the prescription. |
| `sets` | int | ✅ | Number of working sets. |
| `repsMin` | int | — | Minimum of the rep range (e.g. `8` in `"8–10 reps"`). Omit for time-based or distance-based exercises. |
| `repsMax` | int | — | Maximum of the rep range. When equal to `repsMin`, the app displays a single number (`"8 reps"`). |
| `repsText` | string | — | Free-form rep/distance prescription displayed verbatim (e.g. `"40m"`, `"AMRAP"`, `"Max hold"`). Use when `repsMin`/`repsMax` don't apply. Do not include the set count — the app prepends `"N sets × "` automatically. |
| `logMode` | string | — | How sets are logged. Allowed values: `"reps"` (weight + rep count — the default), `"timer"` (countdown from `durationSeconds`; auto-completes at zero), `"duration"` (count-up stopwatch; user stops it manually). Omit to use `"reps"`. |
| `durationSeconds` | int | — | Target duration in seconds. **Required** when `logMode` is `"timer"`. Optional for `"duration"` (sets a suggested target but doesn't enforce it). |
| `perSide` | bool | — | When `true`, the set is performed on each side separately (e.g. single-leg, single-arm). The rep count or timer applies per side. Default: `false`. |
| `load` | string | ✅ | Weight/load guidance displayed to the user (e.g. `"70–80% 1RM"`, `"Bodyweight"`, `"Heavy — RPE 8"`). Use `""` if no guidance applies. |
| `tempo` | string | — | Tempo notation in `"ECCE"` format (eccentric · pause-bottom · concentric · pause-top, e.g. `"30X0"`). `"X"` = explosive. Displayed as a badge on the exercise card. |
| `notes` | string | — | Coaching cue shown below the exercise name during the workout. |
| `priority` | string | ✅ | `"core"` = always performed; `"optional"` = shown with a badge and skippable. Default: `"core"`. Any unrecognised value is treated as `"core"`. |
| `orderIndex` | int | ✅ | 0-based sort position within the group. |
| `restSeconds` | int | — | Rest period in seconds after each set. Overrides the app default of 90 s. |
| `showHeartRateZones` | bool | — | When `true`, a live HR zone bar is displayed below the rest timer. Requires an Apple Watch streaming HR. Default: `false`. |
| `targetZoneLow` | int | — | Lower bound of the target HR zone band (1–5). Used with `showHeartRateZones` and `timerStartMode: "waitForZone"`. |
| `targetZoneHigh` | int | — | Upper bound of the target HR zone band. Omit to target a single zone (`targetZoneLow` only). |
| `timerStartMode` | string | — | When the countdown starts. `"immediate"` (default): starts when the user taps Go. `"waitForZone"`: waits until live HR enters the target zone band, then auto-starts. Only meaningful for `logMode: "timer"` with `targetZoneLow` set. |
| `alternatives` | ExerciseAlternative[] | — | Curated substitutions shown in the exercise swap sheet. The app's exercise database also provides default alternatives; entries here take precedence. See [ExerciseAlternative](#exercisealternative). |
| `citationIds` | string[] | — | IDs of [Citation](#citation) objects that support this exercise's prescription. |

---

## ExerciseAlternative

A curated substitution for a prescribed exercise.

```json
{
  "exerciseId": "dumbbell_floor_press",
  "context": "No bench available",
  "equipmentTags": ["dumbbell"],
  "isIdeal": true
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `exerciseId` | string | ✅ | ID of the alternative exercise. Must exist in `ExerciseDatabase.json` or the program's `exercises` array. |
| `context` | string | — | Human-readable note explaining when to use this alternative (e.g. `"No bench available"`). Shown in the substitution sheet. |
| `equipmentTags` | string[] | — | Equipment required for this alternative (e.g. `["dumbbell", "bench"]`). Used to filter alternatives by the user's equipment profile. Omit or use `[]` for bodyweight. |
| `isIdeal` | bool | — | `false` = lower-stimulus fallback; the app shows a "not ideal" badge. Default (omit): `true`. |

---

## Exercise

Program-specific exercise definitions. Use these to provide coaching notes and citations for exercises that appear in this program's workouts. The `exerciseId` on a `PrescribedExercise` may reference either this array or the global `ExerciseDatabase.json` — this array is checked first.

```json
{
  "id": "trap_bar_deadlift",
  "name": "Trap Bar Deadlift",
  "category": "hinge",
  "equipment": ["trap_bar"],
  "notes": "Stand in the centre of the trap bar. Push the floor away...",
  "citationIds": ["schoenfeld_2017"]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | ✅ | Unique identifier. Must match the `exerciseId` used in prescriptions. |
| `name` | string | ✅ | Display name. |
| `category` | string | ✅ | Movement pattern. Allowed values: `"hinge"`, `"squat"`, `"push"`, `"pull"`, `"carry"`, `"core"`, `"cardio"`, `"mobility"`, `"power"`, `"other"`. |
| `equipment` | string[] | ✅ | Equipment required (e.g. `["dumbbell"]`, `["barbell", "rack"]`, `[]` for bodyweight). |
| `notes` | string | — | Full coaching notes / technique description. |
| `citationIds` | string[] | — | IDs of [Citation](#citation) objects supporting this exercise's selection. |

---

## MobilityRoutine

A mobility or habit routine shown on the Home screen Daily Habits card or as a pre/post-workout checklist within a session.

```json
{
  "id": "morning_cars",
  "name": "Morning CARs Routine",
  "timing": "general",
  "durationDescription": "5–7 min",
  "notes": "Do before coffee. Daily consistency beats occasional long sessions.",
  "themeTag": null,
  "phaseNames": null,
  "exercises": [ ...MobilityExercise ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | ✅ | Stable identifier. Referenced by `preMobilityIds` / `postMobilityIds` on workouts. |
| `name` | string | ✅ | Display name. |
| `timing` | string | ✅ | When this routine is shown. Allowed values: `"general"` (Daily Habits card, always visible), `"pre_workout"` (checklist before the workout), `"post_workout"` (checklist after the workout). |
| `durationDescription` | string | ✅ | Estimated time (e.g. `"5–7 min"`). Shown on the routine card. |
| `notes` | string | — | Coaching note or intent description shown on the routine card. |
| `themeTag` | string | — | Mobility rotation tag. `null` = anchor routine (always shown regardless of phase). `"A"`–`"F"` = phase-themed routine, displayed only when the user's active phase matches (see `phaseNames`). |
| `phaseNames` | string[] | — | Phase names this themed routine applies to (must match `phaseName` values on plans in the same program). `null` = applies to all phases. Only meaningful when `themeTag` is set. |
| `exercises` | MobilityExercise[] | ✅ | Ordered list of [MobilityExercise](#mobilityexercise) items. |

---

## MobilityExercise

A single item within a mobility routine.

```json
{
  "name": "Hip CARs",
  "duration": "5 circles each direction / side",
  "frequency": "Daily",
  "focusArea": "Hip joint health",
  "notes": "Keep the pelvis neutral. Max range, min compensation.",
  "priority": "core",
  "orderIndex": 0
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | ✅ | Exercise name displayed on the checklist row. |
| `duration` | string | ✅ | Prescription text (e.g. `"30 seconds"`, `"3 reps / side"`, `"10 slow reps"`). |
| `frequency` | string | — | How often to do this exercise (e.g. `"Daily"`, `"3×/week"`). Informational. |
| `focusArea` | string | — | Body area or adaptation target (e.g. `"Hip flexor length"`, `"Thoracic rotation"`). Informational. |
| `notes` | string | — | Technique or intent note. |
| `priority` | string | — | `"core"` (always shown) or `"optional"` (shown with an optional badge). Default: `"core"`. |
| `orderIndex` | int | ✅ | 0-based sort position within the routine. |

---

## Benchmark

A performance test tracked on the Home screen benchmarks grid.

```json
{
  "id": "deadlift_pr",
  "name": "Trap Bar Deadlift 3RM",
  "description": "Best triple at competition-legal depth.",
  "type": "exercise_pr",
  "exerciseId": "trap_bar_deadlift",
  "metric": "weight",
  "unit": "lbs",
  "orderIndex": 0,
  "citationIds": [],
  "targets": [
    { "label": "Baseline", "value": 135, "orderIndex": 0 },
    { "label": "Goal",     "value": 225, "orderIndex": 1 }
  ]
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | ✅ | Stable identifier. |
| `name` | string | ✅ | Display name. |
| `description` | string | — | Additional context shown in the benchmark detail view. |
| `type` | string | ✅ | `"exercise_pr"` — tracks the best logged set for a specific exercise. `"standalone_test"` — manually logged results (e.g. VO₂ max, resting HR). |
| `exerciseId` | string | — | Required when `type` is `"exercise_pr"`. Must match an exercise ID in this program or the global DB. |
| `metric` | string | ✅ | What is measured. Allowed values: `"weight"`, `"reps"`, `"time"`, `"distance"`, `"score"`. |
| `unit` | string | ✅ | Display unit (e.g. `"lbs"`, `"kg"`, `"seconds"`, `"metres"`, `"ml/kg/min"`). |
| `orderIndex` | int | ✅ | 0-based sort position on the Home benchmarks grid. |
| `citationIds` | string[] | — | IDs of supporting [Citation](#citation) objects. |
| `targets` | BenchmarkTarget[] | ✅ | Reference points shown as markers on the benchmark progress chart. May be `[]`. See [BenchmarkTarget](#benchmarktarget). |

---

## BenchmarkTarget

A labelled reference value on a benchmark (e.g. population norms, personal goals).

```json
{ "label": "Novice", "value": 135.0, "orderIndex": 0 }
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `label` | string | ✅ | Target label (e.g. `"Baseline"`, `"Goal"`, `"Elite"`). |
| `value` | number | ✅ | Numeric target in the benchmark's `unit`. |
| `orderIndex` | int | ✅ | 0-based sort position within the target list. |

---

## Citation

A research reference that can be linked to exercises or benchmarks via `citationIds`.

```json
{
  "id": "schoenfeld_2019",
  "principle": "Minimum effective volume for hypertrophy",
  "authors": "Schoenfeld BJ, Ogborn D, Krieger JW",
  "journal": "Journal of Strength and Conditioning Research",
  "year": 2019,
  "keyFinding": "Even a single set per muscle group per session produces significant hypertrophy; 3+ sets per week maximises it.",
  "url": "https://doi.org/10.1519/JSC.0000000000002585"
}
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `id` | string | ✅ | Stable identifier referenced by `citationIds` on exercises, benchmarks, and prescribed exercises. |
| `principle` | string | ✅ | Short title of the training principle this paper supports. |
| `authors` | string | ✅ | Author(s) in short form (e.g. `"Schoenfeld BJ et al."`). |
| `journal` | string | ✅ | Publication venue. |
| `year` | int | ✅ | Publication year. |
| `keyFinding` | string | ✅ | One-sentence summary of the finding as it applies to this program. |
| `url` | string | — | DOI or direct link to the paper. |

---

## Validation

The `validate_programs.py` script in the [Pillar iOS repo](https://github.com/Jalsemgeest/workoutapp) cross-references every program JSON against the app's `ExerciseDatabase.json` and checks:

- `exerciseId` values resolve to known exercises
- `logMode` matches the exercise database's `suggestedLogMode` where one is set
- `logMode: "timer"` exercises have `durationSeconds` set
- Required fields are present on every object
- `alternatives` entries are objects (`{exerciseId: ...}`), not bare strings

Run it with:

```bash
python3 Pillar/Scripts/validate_programs.py
```

---

## Minimal example

A valid program with one plan, one workout, one group, and one exercise:

```json
{
  "schemaVersion": "1.0",
  "programs": [
    {
      "id": "my_program",
      "name": "My Program",
      "version": "1.0.0",
      "description": "A simple example program.",
      "tags": ["home", "dumbbell"],
      "targetProfile": "intermediate",
      "createdAt": "2026-01-01",
      "plans": [
        {
          "id": "my_plan",
          "name": "3-Day",
          "scheduleDescription": "Mon · Wed · Fri",
          "daysPerWeek": 3,
          "orderIndex": 0,
          "workouts": [
            {
              "id": "day_a",
              "dayLabel": "Day A",
              "sessionType": "Full Body",
              "durationRange": "45–55 min",
              "location": "Home",
              "keyMetrics": ["Goblet squat weight"],
              "orderIndex": 0,
              "activityType": "strength",
              "groups": [
                {
                  "name": "SQUAT",
                  "orderIndex": 0,
                  "exercises": [
                    {
                      "exerciseId": "goblet_squat",
                      "sets": 3,
                      "repsMin": 8,
                      "repsMax": 10,
                      "load": "Moderate-heavy",
                      "priority": "core",
                      "orderIndex": 0
                    }
                  ]
                }
              ]
            }
          ]
        }
      ],
      "exercises": [],
      "mobilityRoutines": [],
      "benchmarks": [],
      "citations": []
    }
  ]
}
```
