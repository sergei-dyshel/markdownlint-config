# markdownlint-config

Shared [markdownlint](https://github.com/DavidAnson/markdownlint) configuration, consumed by every package's `lint-md` task.

## Structure

`markdownlint.jsonc` is the shared **markdownlint rules** config — a rules-config file whose top-level `extends: "markdownlint/style/prettier"` turns off every rule Prettier already owns (e.g. `MD013` line-length). It is the package `main`, so `@sergei-dyshel/markdownlint-config` resolves to it.

Each consuming package has its own `.markdownlint-cli2.jsonc` (a markdownlint-cli2 **options** file) that sets `"config": { "extends": "@sergei-dyshel/markdownlint-config" }`. The files to lint come from the shared `lint-md` task's globs (`"**/*.md" "#node_modules"`), not from that config — a per-package config with no `globs` lints nothing on its own.

## `config.extends` inherits rules only, never cli2 options

`config.extends` pulls in only markdownlint **rule** settings, read from the _top level_ of its target. It does **not** inherit the markdownlint-cli2 options `globs`/`ignores` — those live in the options file and are never shared through `extends`. And the target must itself be a rules-config file: pointing `config.extends` at another `.markdownlint-cli2.jsonc` (an options file, whose rules sit nested under `config`) silently drops those rules, because markdownlint finds none at the target's top level — so the Prettier style is not applied and `lint-md` passes while still enforcing defaults like `MD013`.
