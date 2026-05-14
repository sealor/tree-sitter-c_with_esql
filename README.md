# tree-sitter-c_with_esql

C grammar with Embedded SQL support for [tree-sitter](https://github.com/tree-sitter/tree-sitter).  
Forked from [tree-sitter-c](https://github.com/tree-sitter/tree-sitter-c).  
Adapted from [this C99 grammar](http://slps.github.io/zoo/c/iso-9899-tc3.html).  

Documentation for Embedded SQL:

- [Embedded SQL - Wikipedia](https://en.wikipedia.org/wiki/Embedded_SQL)
- [Pro*C - Wikipedia](https://en.wikipedia.org/wiki/Pro*C)
- [Introduction to Pro*C Embedded SQL](http://infolab.stanford.edu/%7Eullman/fcdb/oracle/or-proc.html)
- [Oracle  Pro*C Sample Programs](https://download.oracle.com/otn_hosted_doc/timesten/1122/quickstart/html/developer/proc/proc.html)

## Neovim configuration

```lua
-- neovim-0.12.2

vim.pack.add({
	{ src = "https://github.com/folke/which-key.nvim" },
	{ src = "https://github.com/sealor/tree-sitter-c_with_esql" },

	{
		src = "https://github.com/nvim-treesitter/nvim-treesitter",
		version = "4916d6592ede8c07973490d9322f187e07dfefac",
	},
	{
		src = "https://github.com/nvim-treesitter/nvim-treesitter-textobjects",
		version = "851e865342e5a4cb1ae23d31caf6e991e1c99f1e",
	},
})

vim.api.nvim_create_autocmd("User", {
	pattern = "TSUpdate",
	callback = function()
		local package = vim.pack.get({ "tree-sitter-c_with_esql" })[1]

		---@type ParserInfo
		require("nvim-treesitter.parsers").c_with_esql = {
			install_info = {
				path = package.path,
				url = package.spec.src,
				revision = package.rev,
				generate_requires_npm = false,
				requires_generate_from_grammar = false,
			},
			tier = 1,
			maintainers = { "@sealor" },
			filetype = "c_with_esql",
		}
	end,
})

vim.filetype.add({ extension = { pc = "c_with_esql" } })
vim.treesitter.language.register("c_with_esql", { "pc" })
require("nvim-treesitter").install({ "c_with_esql" })

vim.api.nvim_create_autocmd("FileType", {
	pattern = { "c_with_esql" },
	callback = function()
		vim.treesitter.start()
	end,
})

local TOMove = require("nvim-treesitter-textobjects.move")
local TOSelect = require("nvim-treesitter-textobjects.select")
local wk = require("which-key")
wk.add({
    -- stylua: ignore start
    { "[f", function() TOMove.goto_previous_start("@function.outer", "textobjects") end,  mode = { "n", "x", "o" }, desc = "Previous Function" },
    { "]f", function() TOMove.goto_next_start("@function.outer", "textobjects") end,      mode = { "n", "x", "o" }, desc = "Next Function" },
    { "[a", function() TOMove.goto_previous_start("@parameter.inner", "textobjects") end, mode = { "n", "x", "o" }, desc = "Previous Parameter" },
    { "]a", function() TOMove.goto_next_start("@parameter.inner", "textobjects") end,     mode = { "n", "x", "o" }, desc = "Next Parameter" },
    { "aa", function() TOSelect.select_textobject("@parameter.outer", "textobjects") end, mode = { "x", "o" },      desc = "outer Parameter" },
    { "ia", function() TOSelect.select_textobject("@parameter.inner", "textobjects") end, mode = { "x", "o" },      desc = "inner Parameter" },
	-- stylua: ignore env
})
```
