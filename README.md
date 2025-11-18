# lsp-selection-range.nvim

A Neovim plugin that provides incremental selection expansion and shrinking based on LSP `textDocument/selectionRange`.

## Features

- **LSP-based smart selection**: Uses your language server's understanding of code structure
- **Incremental expansion/shrinking**: Gradually expand or shrink selections
- **Universal support**: Works with any LSP server that supports `textDocument/selectionRange`
- **Configurable keybindings**: Customize to your preference
- **Automatic state management**: Clears selection history when exiting visual mode

## Demo

Press `gn` to expand selection to the next semantic level, `gN` to shrink back.

```
word → identifier → expression → statement → block → function → file
```

## Requirements

- Neovim 0.8.0+ (0.10.0+ recommended for better UTF-16 handling)
- An LSP server that supports `textDocument/selectionRange`

## Installation

### [lazy.nvim](https://github.com/folke/lazy.nvim)

```lua
{
  "yourusername/lsp-selection-range.nvim",
  config = function()
    require("lsp-selection-range").setup()
  end,
}
```

### [packer.nvim](https://github.com/wbthomason/packer.nvim)

```lua
use {
  "yourusername/lsp-selection-range.nvim",
  config = function()
    require("lsp-selection-range").setup()
  end,
}
```

### [vim-plug](https://github.com/junegunn/vim-plug)

```vim
Plug 'yourusername/lsp-selection-range.nvim'

lua << EOF
require("lsp-selection-range").setup()
EOF
```

## Usage

### Default Keybindings

In normal or visual mode:

- `gn` - Expand selection to the next outer semantic level
- `gN` - Shrink selection to the previous inner semantic level

### How it works

1. Position your cursor on any text
2. Press `gn` in normal mode to start a selection
3. Press `gn` again to expand the selection outward
4. Press `gN` to shrink the selection inward
5. Exit visual mode (`<Esc>`) to reset - next `gn` will start fresh

## Configuration

### Default Configuration

```lua
require("lsp-selection-range").setup({
  keymaps = {
    expand = "gn",  -- Keymap to expand selection
    shrink = "gN",  -- Keymap to shrink selection
  },
  modes = { "n", "v" },  -- Modes where keymaps are active
  clear_state_on_mode_exit = true,  -- Clear selection history when exiting visual mode
})
```

### Custom Keybindings

```lua
require("lsp-selection-range").setup({
  keymaps = {
    expand = "<leader>n",
    shrink = "<leader>N",
  },
})
```

### Visual Mode Only

```lua
require("lsp-selection-range").setup({
  modes = { "v" },  -- Only activate in visual mode
})
```

### Disable Auto-Clear

```lua
require("lsp-selection-range").setup({
  clear_state_on_mode_exit = false,  -- Keep selection history across mode changes
})
```

## LSP Server Support

This plugin works with any LSP server that implements `textDocument/selectionRange`. Known supported servers include:

- **TypeScript/JavaScript**: `typescript-language-server`
- **Python**: `pyright`, `pylsp`
- **Rust**: `rust-analyzer`
- **Go**: `gopls`
- **C/C++**: `clangd`
- **Java**: `jdtls`
- **HTML/CSS**: `vscode-html-language-server`, `vscode-css-language-server`
- **Lua**: `lua-language-server`
- **And many more...**

Check your LSP server's documentation for `textDocument/selectionRange` support.

## How It Works

1. **First invocation**: Queries the LSP server for the selection range hierarchy at your cursor position
2. **Caching**: Stores the entire hierarchy (from smallest to largest range) in memory
3. **Navigation**: Subsequent presses navigate through the cached hierarchy without re-querying
4. **State reset**: When you exit visual mode, the cache is cleared for the next selection

## Comparison with Other Plugins

### vs. `nvim-treesitter/nvim-treesitter-textobjects`

- **treesitter**: Uses Treesitter AST, requires language-specific queries
- **lsp-selection-range**: Uses LSP semantic understanding, works across all LSP-enabled languages

### vs. `vim-expand-region`

- **vim-expand-region**: Uses regex-based heuristics
- **lsp-selection-range**: Uses actual language server semantic analysis

## Troubleshooting

### Keybindings don't work

Check if your LSP server supports `textDocument/selectionRange`:

```lua
:lua vim.print(vim.lsp.get_clients()[1].server_capabilities.selectionRangeProvider)
```

Should return `true` or a table, not `nil`.

### Off-by-one errors with UTF-16 characters

Upgrade to Neovim 0.10.0+ for proper UTF-16 character handling.

### Selection doesn't clear

Ensure `clear_state_on_mode_exit = true` (default) in your config.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

MIT License - see [LICENSE](LICENSE) file for details.

## Acknowledgments

- Inspired by VS Code's "Expand Selection" feature
- Built on Neovim's excellent LSP infrastructure
