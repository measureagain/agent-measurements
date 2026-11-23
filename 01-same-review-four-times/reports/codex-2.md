1. **Existing calls can silently lose a keyword intended for a custom command (medium).** Before this change, `invoke(cli, context_settings=...)` forwarded `context_settings` to an overridden `Command.main()`. The new parameter intercepts the keyword and expands its contents. I verified that a custom `main(context_settings=None, **kwargs)` receives the mapping at the base commit and receives `None` at HEAD. [src/click/testing.py:634](src/click/testing.py:634)

2. **`Command.main()` can consume a setting intended for a custom context (medium).** I verified that `context_settings={"standalone_mode": False}` changes how `main()` handles exits while leaving a custom context’s `standalone_mode` setting at its command-declared default. The same key reaches the custom context when declared on the command. The override test covers `terminal_width`, so it misses this collision. [src/click/testing.py:726](src/click/testing.py:726)

3. **A nonempty mapping that evaluates to false is silently ignored (low).** `context_settings or {}` discards any falsy mapping. I verified that a `dict` subclass with a false `__bool__` and a `color=True` entry leaves the command’s `color=False` default in effect. [src/click/testing.py:730](src/click/testing.py:730)

4. **The testing guide overstates when context color is unset (low).** The guide says `invoke(color=True)` leaves the context’s color unset. If the command declares `context_settings={"color": False}`, the context still gets `False` and strips ANSI styling. [docs/testing.md:125](docs/testing.md:125)

5. **The public documentation omits the duplicate-key error (low).** The docstring explains precedence over command defaults but omits what happens when a setting is also passed directly to `invoke()`. The new duplicate-`obj` test explicitly expects a `TypeError` in that case. [src/click/testing.py:657](src/click/testing.py:657)

6. **New test assertions use conditional expressions prohibited by the contributor guide (low).** The prompt and runner tests both use ternaries to construct expected output. The guide requires explicit `if`/`else` blocks so coverage can measure both branches. [tests/test_termui.py:1447](tests/test_termui.py:1447), [tests/test_testing.py:515](tests/test_testing.py:515)

7. **New Markdown prose exceeds the 80-character limit (low).** Changelog line 26 and testing guide lines 123–125 and 152 exceed the contributor guide’s wrap limit. [CHANGES.md:26](CHANGES.md:26), [docs/testing.md:123](docs/testing.md:123)

I did not verify the pytest suite, the rendered documentation, or platform-specific behavior.
