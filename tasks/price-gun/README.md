# Price three spice bottles with a price gun

Three small spice bottles stand in a row, and a hand labeller (a price gun) lies on its side beside them. Each full
squeeze of the gun's trigger feeds one label, which is wiped onto a bottle's cap with the roller at the gun's nose.
One arm works the gun while the other holds the bottle, which is light enough to tip at a touch.

## Files

- `task.py`: scene, label model, scoring and scripted demo (yam_arena task file). A step hook winds the trigger's
  spring past its rest stop (a preload), runs the label model and prints lines starting `PG|` (state, every 5 control
  steps) and `PG!` (label events).
- `labkit.py`: parts built from several shapes, with hinges and slides. The gun's trigger is a sprung hinge and its
  roller a hinge. It hooks `yam_arena.scene` and the embodiment from the task's import, so no change to yam_arena is
  needed. This copy adds display-only shapes (`DISPLAY`), used to draw the labels.
- `arena_assets_fix.py`: imported by `labkit.py`; points Arena library assets at NVIDIA's production bucket. This task
  uses no library assets.
- `task.json`: the task's specification.

## Run a trial

Inside the Isaac Lab-Arena environment with `yam-isaac-lab-arena` installed (the Robocurve Brev launchable):

    inspect-robots run --embodiment yam_arena --policy dream \
        -E task=$PWD/task.py -E object_jitter=0 -E control_hz=20 \
        --instruction "$(python -c 'import json;print(json.load(open("task.json"))["instruction"])')" \
        --scorer task_score --max-steps 2400 --store-frames --log-dir logs

Scripted demo: the same command with `--policy yam_scripted` (it takes 62 s, 1,246 control steps).

The score is `task_score` in the log, computed by `score()` in `task.py`.

## How the demo holds the gun

One gripper straddles the handle (behind) and the trigger (in front). Closing fully is a full stroke and a firm hold;
opening part-way lets the trigger out and still holds the gun, because the trigger's spring is wound. The arm's drives
are soft and carry the gun's 4 N, so the demo aims each pose of the loaded arm high by the sag a static model predicts.

## Runtime and seeds

- 2,400 control steps (2 minutes of simulated time) per trial.
- One fixed starting layout: run with `-E object_jitter=0`. No other randomness.

## Third-party assets

None. Every shape is built in `task.py` from boxes and cylinders.
