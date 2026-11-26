---
status: backlog
---
## Description

Ukkei Scenes is a command-line scene engine for Ukkei, a smart-home app that puts every smart device in one app. It loads mock devices (lights and plugs, grouped by room) and scenes (Goodnight, Movie night, Morning) from YAML files, applies a scene, and prints each device's change as `before -> after`. Devices a scene doesn't mention stay unchanged. Invalid scenes are rejected with a clear error, each applied scene is recorded in an activity log file, and the same input files always give the same output. It is built from scratch in Python with pytest and ruff, with no network and no real devices.

## Agent instructions

1. Read `AGENTS.md`, and `work/TKT-01 Build a tabletop dice roller.md` for the shape of a finished plan. The repo has no code yet, so the first commit sets up the project.
2. Propose these decisions to the person, with options and trade-offs, and wait for their sign-off before writing any code:
   - The project folder name.
   - The YAML library. PyYAML is the likely choice, and it is the project's one runtime dependency.
   - The command-line shape, such as `ukkei-scenes apply "Movie night" --devices devices.yaml --scenes scenes.yaml --log activity.log`.
   - The YAML layout for devices (grouped by room) and for scenes.
   - The activity log format. The log must not hold wall-clock timestamps, or the same inputs would give different output. Propose a deterministic entry, such as the scene name and its changes.
   - Whether applying a scene saves the new device states back to the devices file. By default it doesn't, so each run starts from the same files.
3. Propose the sample data to the person: the 8 devices across living room, bedroom and kitchen (lights and plugs), and the three scenes. Wait for their sign-off before writing it.
4. Work the commit plan one row at a time, as `AGENTS.md` describes. Validation errors must name the scene and the device, and say what was wrong.
5. Run the post-commit tests once every commit has landed.

## Commit plan

| # | Commit message | What it covers | Status |
| --- | --- | --- | --- |
| 1 | `chore(ukkei): set up project with pytest and ruff (TKT-06)` | Folder, `pyproject.toml`, empty package, one smoke test, ruff config | |
| 2 | `feat(ukkei): load devices from yaml (TKT-06)` | Light and plug models, loading grouped by room, the 8 sample devices, tests | |
| 3 | `feat(ukkei): load scenes from yaml (TKT-06)` | Scene model and loader, the Goodnight, Movie night and Morning scenes, tests | |
| 4 | `feat(ukkei): reject invalid scenes (TKT-06)` | Validation: unknown device, brightness outside 0-100, colour on a plug, with clear errors, tests | |
| 5 | `feat(ukkei): apply a scene to devices (TKT-06)` | Apply function returning each device's before and after state, unmentioned devices unchanged, tests | |
| 6 | `feat(ukkei): add apply command (TKT-06)` | Command-line entry point that prints each change as `before -> after`, and a readable error and non-zero exit on invalid input, tests | |
| 7 | `feat(ukkei): record applied scenes in activity log (TKT-06)` | Append one deterministic entry per applied scene to the log file, tests | |
| 8 | `docs(ukkei): document usage and sample files (TKT-06)` | Project README with install, run, file formats and a worked example | |

## Post-commit testing

| # | Check | How | Status |
| --- | --- | --- | --- |
| 1 | The checks pass | Run pytest and `ruff check` in the project folder. Both should pass with no errors. | |
| 2 | Each sample scene applies | Run `apply` for Goodnight, Movie night and Morning against the sample files. Each prints one `before -> after` line per changed device, and nothing for the others. | |
| 3 | Unmentioned devices are unchanged | Apply Movie night and compare with the devices file. Devices not in the scene are not in the output. | |
| 4 | Invalid scenes are rejected | Try a scene with an unknown device, brightness 150, and a colour on a plug. Each gives a clear error naming the scene and the device, and a non-zero exit. | |
| 5 | Output is repeatable | Run the same apply twice, on a fresh log each time. The output and the log entries are identical. | |
| 6 | The activity log records scenes | Apply two scenes, then read the log. It has one entry per applied scene, in order. A rejected scene adds no entry. | |

## Acceptance criteria

- [ ] The person signed off the folder name, the YAML library, the command-line shape and the file layouts
- [ ] The person signed off the activity log format and whether state is saved back
- [ ] The person signed off the sample devices and scenes
- [ ] The person signed off the commit plan
- [ ] The 8 sample devices load, grouped by room, with lights and plugs in the living room, bedroom and kitchen
- [ ] Goodnight, Movie night and Morning load from a YAML file and apply, printing `before -> after` for each changed device
- [ ] Devices a scene doesn't mention are unchanged and not printed as changed
- [ ] An unknown device, brightness outside 0-100, and colour on a plug are each rejected with a clear error
- [ ] Each applied scene is recorded in the activity log file, and a rejected scene is not
- [ ] The same input files always give the same output, including the log
- [ ] pytest and ruff pass, and the project needs no network access

## Stretch goals

- A `list` command that shows every device by room
- A `--dry-run` flag that prints the changes without writing to the log
- Colour temperature and colour validated against a fixed set of allowed values
