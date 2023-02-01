load("@rules_python//python:pip.bzl", "compile_pip_requirements")

compile_pip_requirements(
    name = "py_deps",
    requirements_in = "requirements.in",
    requirements_txt = "requirements.lock.txt",
	extra_args = [
		"--allow-unsafe",
	],
)

load("@rules_python//python:defs.bzl", "py_binary")

py_binary(
	name = "foo",
	srcs = ["foo.py"],
	deps = [
		"@pip_ray//:pkg",
	]
)

py_binary(
	name = "bar",
	srcs = ["bar.py"],
	deps = [
		"@pip_ray//:pkg",
	]
)
