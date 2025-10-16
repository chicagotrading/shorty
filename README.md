# aspect_rules_py venv bug repro

## activating venv prevents running py_binary targets

Before activating, `bazel run <py_binary>` works as expected:
```
$ bazel run //app:app_bin
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

After activating, not so much:
```
$ bazel run //app:app_bin.venv
...
$ source .app+app_bin.venv/bin/activate
$ bazel run //app:app_bin
INFO: Analyzed target //app:app_bin (1 packages loaded, 5 targets configured).
INFO: Found 1 target...
Target //app:app_bin up-to-date:
  bazel-bin/app/app_bin
  bazel-bin/app/app_bin.venv.pth
INFO: Elapsed time: 0.172s, Critical Path: 0.00s
INFO: 2 processes: 1 action cache hit, 2 internal.
INFO: Build completed successfully, 2 total actions
INFO: Running command line: bazel-bin/app/app_bin
Error:   × Unable to run command:
  ╰─▶ Querying Python at `/private/var/tmp/_bazel_joshua.bronson/3128aa25a1a2a07e424bf6a1e3476f9d/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/app/app_bin.runfiles/
      rules_python++python+python_3_12_aarch64-apple-darwin/bin/python3` failed with exit status exit status: 1
      --- stdout:

      --- stderr:
      Could not find platform independent libraries <prefix>
      Could not find platform dependent libraries <exec_prefix>
      Python path configuration:
        PYTHONHOME = (not set)
        PYTHONPATH = (not set)
        program name = '/private/var/tmp/_bazel_joshua.bronson/3128aa25a1a2a07e424bf6a1e3476f9d/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/app/app_bin.runfiles/
      rules_python++python+python_3_12_aarch64-apple-darwin/bin/python3'
        isolated = 1
        environment = 0
        user site = 0
        safe_path = 1
        import site = 1
        is in build tree = 0
        stdlib dir = '/install/lib/python3.12'
        sys._base_executable = '/private/var/tmp/_bazel_joshua.bronson/3128aa25a1a2a07e424bf6a1e3476f9d/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/app/
      app_bin.runfiles/rules_python++python+python_3_12_aarch64-apple-darwin/bin/python3'
        sys.base_prefix = '/install'
        sys.base_exec_prefix = '/install'
        sys.platlibdir = 'lib'
        sys.executable = '.app+app_bin.venv/bin/python'
        sys.prefix = '/install'
        sys.exec_prefix = '/install'
        sys.path = [
          '/install/lib/python312.zip',
          '/install/lib/python3.12',
          '/install/lib/python3.12/lib-dynload',
        ]
      Fatal Python error: init_fs_encoding: failed to get the Python codec of the filesystem encoding
      Python runtime state: core initialized
      ModuleNotFoundError: No module named 'encodings'

      Current thread 0x00000001f402e0c0 (most recent call first):
        <no Python frame>
      ---
```
Deactivating doesn't help:
```
$ deactivate
$ bazel run //app:app_bin
INFO: Analyzed target //app:app_bin (1 packages loaded, 5 targets configured).
INFO: Found 1 target...
Target //app:app_bin up-to-date:
  bazel-bin/app/app_bin
  bazel-bin/app/app_bin.venv.pth
INFO: Elapsed time: 0.148s, Critical Path: 0.00s
INFO: 1 process: 1 internal.
INFO: Build completed successfully, 1 total action
INFO: Running command line: bazel-bin/app/app_bin
Error:   × Unable to run command:
  ╰─▶ Querying Python at `/private/var/tmp/_bazel_joshua.bronson/3128aa25a1a2a07e424bf6a1e3476f9d/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/app/app_bin.runfiles/
      rules_python++python+python_3_12_aarch64-apple-darwin/bin/python3` failed with exit status exit status: 1
      --- stdout:

      --- stderr:
      Could not find platform independent libraries <prefix>
      Could not find platform dependent libraries <exec_prefix>
      Python path configuration:
        PYTHONHOME = (not set)
        PYTHONPATH = (not set)
        program name = '/private/var/tmp/_bazel_joshua.bronson/3128aa25a1a2a07e424bf6a1e3476f9d/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/app/app_bin.runfiles/
      rules_python++python+python_3_12_aarch64-apple-darwin/bin/python3'
        isolated = 1
        environment = 0
        user site = 0
        safe_path = 1
        import site = 1
        is in build tree = 0
        stdlib dir = '/install/lib/python3.12'
        sys._base_executable = '/private/var/tmp/_bazel_joshua.bronson/3128aa25a1a2a07e424bf6a1e3476f9d/execroot/_main/bazel-out/darwin_arm64-fastbuild/bin/app/
      app_bin.runfiles/rules_python++python+python_3_12_aarch64-apple-darwin/bin/python3'
        sys.base_prefix = '/install'
        sys.base_exec_prefix = '/install'
        sys.platlibdir = 'lib'
        sys.executable = '.app+app_bin.venv/bin/python'
        sys.prefix = '/install'
        sys.exec_prefix = '/install'
        sys.path = [
          '/install/lib/python312.zip',
          '/install/lib/python3.12',
          '/install/lib/python3.12/lib-dynload',
        ]
      Fatal Python error: init_fs_encoding: failed to get the Python codec of the filesystem encoding
      Python runtime state: core initialized
      ModuleNotFoundError: No module named 'encodings'

      Current thread 0x00000001f402e0c0 (most recent call first):
        <no Python frame>
```
