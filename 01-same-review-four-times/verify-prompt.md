You are checking review findings against code, read-only. Do not edit any file.

The repository is Click (pallets/click) at commit 90a2973f, the head of pull request 3896, "Add `context_settings` parameter to `CliRunner.invoke`". The change is `git diff ac0e297ec2d7d78455a7d18ce306996e668c4a85..HEAD`. The contributing guide is in the repository.

Sixteen findings were reported by reviewers. For each one, read the code and decide whether the problem it describes is real in this change. You may run Python to reproduce behavior if your environment allows it. A finding about style counts as real only if the project's own guide or the surrounding code sets that rule.

1. The new `context_settings` keyword of `CliRunner.invoke` intercepts an argument that, before this change, was forwarded through `**extra` to a custom `Command.main` or a custom `Context` that accepts a `context_settings` keyword. Behavior changes silently for such callers.
2. The mapping is unpacked into `Command.main()`, so keys named like `main`'s own parameters (`standalone_mode`, `prog_name`, `args`, `complete_var`, `windows_expand_args`) change `main`'s behavior or raise a duplicate-keyword `TypeError`, and never reach the context.
3. `context_settings or {}` discards a non-empty mapping whose `__bool__` returns false.
4. The docs and docstring do not say that passing the same key in `**extra` and in `context_settings` raises `TypeError` (which the runner captures in `Result.exception` by default).
5. The testing guide says `invoke(color=True)` leaves the context's color unset, but a command declaring `context_settings={"color": False}` still gets `ctx.color is False`.
6. New tests use conditional expressions (ternaries) to build expected output, against the contributor guide's rule for explicit `if`/`else`.
7. New Markdown lines exceed the contributor guide's 80-character limit (CHANGES.md line 26, docs/testing.md lines 123 to 125 and 152).
8. The terminal-width example in docs/testing.md passes even when `terminal_width=60` is removed, because the command never emits width-dependent output.
9. The test `test_context_settings_duplicates_extra` matches CPython's error message text ("multiple values for keyword argument"), which is not Click's own behavior and can change between Python versions.
10. `test_tempfile_pager_handles_ansi` now runs every case inside `runner.invoke`, so the path where `echo_via_pager` is called with no context is no longer covered.
11. The diff removes `assert 'Debug mode is on' in result.output` from a docs example, an edit unrelated to this change and not mentioned in the changelog.
12. The changelog entry is hard to parse ("to allow passing settings ... which cannot be given as extra keyword arguments like `color`").
13. The prompt and confirm test in tests/test_termui.py was rewritten more than this change needs, into nested parametrizations that are harder to read.
14. The intro text in docs/testing.md and the `invoke` docstring intro still describe only `**extra` and were not updated to mention `context_settings`.
15. The new `:param context_settings:` docstring entry uses a different continuation indent than the neighbouring params.
16. No test covers `context_settings` with groups or subcommands, so it is unknown whether a setting reaches subcommand contexts.

Reply with only this JSON, one entry per number:

{"verdicts": [{"n": 1, "real": "yes, no or unsure", "why": "one sentence naming the file and line you checked, and whether you reproduced it"}]}
