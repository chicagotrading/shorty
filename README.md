# aspect_rules_py venv bug repro

## 1st-party deps not importable within aspect_rules_py venv

1st-party deps *are* importable when run via Bazel:
```
bash-5.2$ bazel run //app:app_bin
INFO: Analyzed target //app:app_bin (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //app:app_bin up-to-date:
  bazel-bin/app/app_bin
  bazel-bin/app/app_bin.venv.pth
INFO: Elapsed time: 0.134s, Critical Path: 0.00s
INFO: 1 process: 1 internal.
INFO: Build completed successfully, 1 total action
INFO: Running command line: bazel-bin/app/app_bin
hi from foo_lib
Hi from __main__
```

Now create and activate the associated venv:
```
bash-5.2$ bazel run //app:app_bin.venv
INFO: Analyzed target //app:app_bin.venv (0 packages loaded, 0 targets configured).
INFO: Found 1 target...
Target //app:app_bin.venv up-to-date:
  bazel-bin/app/app_bin.venv
INFO: Elapsed time: 0.152s, Critical Path: 0.00s
INFO: 2 processes: 2 action cache hit, 2 internal.
INFO: Build completed successfully, 2 total actions
INFO: Running command line: bazel-bin/app/app_bin.venv


Linking: /private/var/tmp/_bazel_joshua.bronson/3128aa25a1a2a07e424bf6a1e3476f9d/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/app/app_bin.venv.runfiles/_main/app/.app_bin.venv -> /Users/joshua.bronson/clones/oss/shorty/.app+app_bin.venv

Link is up to date!

To configure the virtualenv in your IDE, configure an interpreter with the homedir
    /Users/joshua.bronson/clones/oss/shorty/.app+app_bin.venv

    Please note that you may encounter issues if your editor doesn't evaluate
    the `activate` script. If you do please file an issue at
    https://github.com/aspect-build/rules_py/issues/new?template=BUG-REPORT.yaml

To activate the virtualenv in your shell run
    source /Users/joshua.bronson/clones/oss/shorty/.app+app_bin.venv/bin/activate

virtualenvwrapper users may further want to
    $ ln -s /Users/joshua.bronson/clones/oss/shorty/.app+app_bin.venv $WORKON_HOME/.app+app_bin.venv

bash-5.2$ source /Users/joshua.bronson/clones/oss/shorty/.app+app_bin.venv/bin/activate
```

1st-party deps *are not* importable from within the venv:
```
bash-5.2$ python3 -P -m app
/Users/joshua.bronson/clones/oss/shorty/tools/.app+app_bin.venv/bin/python3: No module named app

bash-5.2$ python3 -P -c "import app.foo_lib"
Traceback (most recent call last):
  File "<string>", line 1, in <module>
ModuleNotFoundError: No module named 'app'
```

Note: You must either pass `-P` (for isolated mode) as above,
or `cd` into a different directory before invoking `python3`
to reproduce this.

Otherwise Python adds `.` to the `PYTHONPATH`, so first-party imports
may happen to work when the directory you're in has matching subdirectories.

With standard Python virtualenvs, after you activate,
first-party deps are importable even with `-P` / no matter what directory you're in.
