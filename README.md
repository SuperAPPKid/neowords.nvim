# Neowords.nvim

<p align="center">
  <a href="https://github.com/backdround/neowords.nvim/actions">
    <img src="https://img.shields.io/github/actions/workflow/status/backdround/neowords.nvim/tests.yaml?branch=main&label=Tests&style=flat-square" alt="Tests">
  </a>
  <a href="https://github.com/backdround/neowords.nvim/actions">
    <img src="https://img.shields.io/github/actions/workflow/status/backdround/neowords.nvim/docs.yaml?branch=main&label=Doc%20generation&status=gen&style=flat-square" alt="Tests">
  </a>
</p>


It's a Neovim plugin that provides flexible and reliable hops by any type
of words. It reimplements basic `w`, `e`, `b`, `ge` movements but
allows a user to have a fine tune over them.

It:
- treats `corner cases` well (`cw` and others);
- works with `non-ascii` text;
- works in `insert` mode;
- uses `foldopen:hor` option to deal with folds.

If you can't configure your case or think there is a useful
preset that isn't presented in the show cases, make an issue!

---

## Show cases
### Sub words only
```lua
-- Ignore anything except sub words!
local CamelCase = "snake_UPPER_MIXEDCase" .. "1234"
--    ^    ^       ^     ^     ^    ^         ^
```
<details><summary>Configuration</summary>

```lua
local neowords = require("neowords")
local p = neowords.pattern_presets

local subword_hops = neowords.get_word_hops(
  p.snake_case,
  p.camel_case,
  p.upper_case,
  p.number,
  p.hex_color
)

vim.keymap.set({ "n", "x", "o" }, "w", subword_hops.forward_start)
vim.keymap.set({ "n", "x", "o" }, "e", subword_hops.forward_end)
vim.keymap.set({ "n", "x", "o" }, "b", subword_hops.backward_start)
vim.keymap.set({ "n", "x", "o" }, "ge", subword_hops.backward_end)
```

</details>

<br>

### Big words only
```lua
-- Ignore anything except big words!
local CamelCase = "snake_UPPER_MIXEDCase" .. "1234" .. "dashed-case"
--    ^            ^                          ^         ^
```
<details><summary>Configuration</summary>

```lua
local neowords = require("neowords")
local p = neowords.pattern_presets

local bigword_hops = neowords.get_word_hops(
  p.any_word,
  p.hex_color
)

vim.keymap.set({ "n", "x", "o" }, "w", bigword_hops.forward_start)
vim.keymap.set({ "n", "x", "o" }, "e", bigword_hops.forward_end)
vim.keymap.set({ "n", "x", "o" }, "b", bigword_hops.backward_start)
vim.keymap.set({ "n", "x", "o" }, "ge", bigword_hops.backward_end)
```

</details>

<br>

### Sub words with dots and commas
```lua
-- Jump over subwords, dots and commas!
error("Unable to " .. description .. ": " .. error_message, 2)
--     ^      ^    ^  ^           ^       ^  ^     ^      ^ ^
```
<details><summary>Configuration</summary>

```lua
local neowords = require("neowords")
local p = neowords.pattern_presets

local hops = neowords.get_word_hops(
  p.snake_case,
  p.camel_case,
  p.upper_case,
  p.number,
  p.hex_color,
  "\\v\\.+",
  "\\v,+"
)

vim.keymap.set({ "n", "x", "o" }, "w", hops.forward_start)
vim.keymap.set({ "n", "x", "o" }, "e", hops.forward_end)
vim.keymap.set({ "n", "x", "o" }, "b", hops.backward_start)
vim.keymap.set({ "n", "x", "o" }, "ge", hops.backward_end)
```

</details>

<br>

### Numbers and hex color words
```lua
-- Ignore anything except number and color words!
local CamelCase = "99 snake_UPPER_MIXEDCase" .. "1234" .. "dashed-case"
--                 ^                             ^
local my_cool_color = "#E3B8EF"
--                     ^
```
<details><summary>Configuration</summary>

```lua
local neowords = require("neowords")
local p = neowords.pattern_presets

local hops = neowords.get_word_hops(
  p.number,
  p.hex_color
)

vim.keymap.set({ "n", "x", "o" }, "w", hops.forward_start)
vim.keymap.set({ "n", "x", "o" }, "e", hops.forward_end)
vim.keymap.set({ "n", "x", "o" }, "b", hops.backward_start)
vim.keymap.set({ "n", "x", "o" }, "ge", hops.backward_end)
```

</details>


---

## Presets

Presets are prepared patterns to use by convenient names. Check real patterns
that stand for the names
[here](https://github.com/backdround/neowords.nvim/blob/main/lua/neowords/pattern-presets.lua).

<details><summary>Showcase</summary>

```
Data to match:
-- showcase_string-FOR_MATCHING_ByDIFFERENTPresets554
-- KNumber number99inside -help -999 +413 12
-- #C3A9FA #a3a9fa #2a3a4c #IF #define #def #Stop
-- word-32 word+33 word_34
```

| Preset name | Matches
| - | -
| snake_case | `showcase`, `string`, `number`, `inside`, `help`, `define`, `word`, `word`, `word`
| camel_case | `By`, `Presets`, `Number`, `Stop`
| upper_case | `FOR`, `MATCHING`, `DIFFERENT`, `K`, `IF`
| number | `554`, `99`, `999`, `413`, `12`, `32`, `33`, `34`
| math_number | `554`, `99`, `-999`, `+413`, `12`, `-32`, `+33`, `34`
| hex_color | `#C3A9FA`, `#a3a9fa`, `#2a3a4c`, `#def`
| any_word | `showcase_string-FOR_MATCHING_ByDIFFERENTPresets554`, `KNumber`, `number99inside`, `help`, `999`, `413`, `12`, `C3A9FA`, `a3a9fa`, `2a3a4c`, `IF`, `define`, `def`, `Stop`, `word-32`, `word`, `33`, `word_34`

</details>

---

## Configure as you wish

```lua
local neowords = require("neowords")
local presets = neowords.pattern_presets

local hops = neowords.get_word_hops(
  -- Vim-patterns or pattern presets separated by commas.
  -- Check `:magic` and onwards for patterns overview.
  "\\v[[:lower:]]+",
  presets.number
)

vim.keymap.set({ "n", "x", "o" }, "w", hops.forward_start)
vim.keymap.set({ "n", "x", "o" }, "e", hops.forward_end)
vim.keymap.set({ "n", "x", "o" }, "b", hops.backward_start)
vim.keymap.set({ "n", "x", "o" }, "ge", hops.backward_end)
```
