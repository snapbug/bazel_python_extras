Reproducing an issue with python extras and bazel.

# What happens
1. `bazel run //:py_deps.update`
2. `bazel run //:foo`

```
INFO: Analyzed target //:foo (98 packages loaded, 14805 targets configured).
INFO: Found 1 target...
Target //:foo up-to-date:
  bazel-bin/foo
INFO: Elapsed time: 40.047s, Critical Path: 2.49s
INFO: 4 processes: 4 internal.
INFO: Build completed successfully, 4 total actions
INFO: Running command line: bazel-bin/foo
Traceback (most recent call last):
  File "/private/var/tmp/_bazel_mcrane/13baa2ea46f1177a54f65afb9fe31f59/execroot/__main__/bazel-out/darwin-fastbuild/bin/foo.runfiles/__main__/foo.py", line 1, in <module>
    import ray.train as train
ModuleNotFoundError: No module named 'ray.train'
```

I see from inspecting `./bazel-out/darwin-fastbuild/bin/foo.runfiles` there are plenty of `pip_*` folders, some of which are included only in extras dependencies, so clearly the extras take effect at install, but can't resolve at run time?

# What's expected
1. Install 3.10.6 using pyenv: `pyenv install 3.10.6`
2. Created a new virtualenv: `pyenv virtualenv 3.10.6 extras-repro`
3. Active the virtualenv: `pyenv local extras-repro`
4. Use the lockfile generated by bazel to install requirements: `pip install -r requirements.lock.txt`
5. `python foo.py`

```
['BackendConfig', 'TRAIN_DATASET_KEY', 'TrainingIterator', '__all__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', '_internal', 'backend', 'base_trainer', 'constants', 'error', 'get_dataset_shard', 'load_checkpoint', 'local_rank', 'report', 'save_checkpoint', 'session', 'train_loop_utils', 'trainer', 'usage_lib', 'world_rank', 'world_size']
```
