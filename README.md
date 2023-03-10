Reproducing an issue with python extras and bazel.

# What happens

1. `bazel run //:py_deps.update`
2. `bazel run //:foo`
```
Traceback (most recent call last):
  File "/private/var/tmp/_bazel_mcrane/13baa2ea46f1177a54f65afb9fe31f59/execroot/__main__/bazel-out/darwin-fastbuild/bin/foo.runfiles/__main__/foo.py", line 2, in <module>
    from ray import serve
ImportError: cannot import name 'serve' from 'ray' (/private/var/tmp/_bazel_mcrane/13baa2ea46f1177a54f65afb9fe31f59/execroot/__main__/bazel-out/darwin-fastbuild/bin/foo.runfiles/pip_ray_cpp/site-packages/ray/__init__.py)
```

I see from inspecting `./bazel-out/darwin-fastbuild/bin/foo.runfiles` there are
plenty of `pip_*` folders, some of which are included only in extras
dependencies, so clearly the extras take effect at install, but can't resolve
at run time?

# What's expected

1. Install 3.10.6 using pyenv: `pyenv install 3.10.6`
2. Created a new virtualenv: `pyenv virtualenv 3.10.6 extras-repro`
3. Active the virtualenv: `pyenv local extras-repro`
4. Use the lockfile generated by bazel to install requirements: `pip install -r requirements.lock.txt`
5. `python foo.py`

```
['HTTPOptions', 'PredictorDeployment', '__all__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', '_private', 'air_integrations', 'api', 'application', 'batch', 'batching', 'config', 'context', 'controller', 'deployment', 'deployment_graph', 'drivers_utils', 'exceptions', 'generated', 'get_deployment', 'get_replica_context', 'handle', 'ingress', 'list_deployments', 'ray', 'run', 'schema', 'shutdown', 'start']
```

# Other

The `:bar` target has another example, using a different import from
the lib. Similarly to the `:foo` target, this works with `python bar.py`. I
include it as another example because the error message is different:

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

