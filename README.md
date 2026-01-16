# VibeVim

VibeVim is a Neovim configuration 

I have never been a fan of "vibe coding tools," but I am also aware of the prevalence of them in the software engineering industry.
If it (ever) becomes the future, I would rather

(a) be aware of its capability and how they work
(b) use Neovim rather than a VS Code-based editor

I started with a simple `init.lua` containing the installation for [avante.nvim](https://github.com/yetone/avante.nvim) and [lazy.nvim](https://github.com/folke/lazy.nvim), along with the initial `avante.md` file.
From there, I used mostly avante.nvim to build the configuration.
All the project instructions, prompts, and other manual interventions (if any) will be documented here.

If you want my Neovim configuration without any AI tools and usage (why would I ever use an AI to write my config when it is my hobby?), check out my [dotfiles repository](https://github.com/theopn/dotfiles).

## Installation

```sh
git clone https://github.com/theopn/vibevim.git ~/.config/vibevim
```

(optional) create the following alias in your shell configuration:

```sh
alias vibevim="NVIM_APPNAME=vibevim nvim"
```

## Development

## Phase 1 (2026-01-16)

Created following initial configuration files:

- `init.lua`:
    ```lua
    require("config.lazy")
    ```
-  `./lua/config/lazy.lua`: [lazy.nvim bootstrap](https://lazy.folke.io/installation)
    ```lua
    -- Bootstrap lazy.nvim
    local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
    if not (vim.uv or vim.loop).fs_stat(lazypath) then
      local lazyrepo = "https://github.com/folke/lazy.nvim.git"
      local out = vim.fn.system({ "git", "clone", "--filter=blob:none", "--branch=stable", lazyrepo, lazypath })
      if vim.v.shell_error ~= 0 then
        vim.api.nvim_echo({
          { "Failed to clone lazy.nvim:\n", "ErrorMsg" },
          { out, "WarningMsg" },
          { "\nPress any key to exit..." },
        }, true, {})
        vim.fn.getchar()
        os.exit(1)
      end
    end
    vim.opt.rtp:prepend(lazypath)
    
    -- Make sure to setup `mapleader` and `maplocalleader` before
    -- loading lazy.nvim so that mappings are correct.
    -- This is also a good place to setup other settings (vim.opt)
    vim.g.mapleader = " "
    vim.g.maplocalleader = "\\"
    
    -- Setup lazy.nvim
    require("lazy").setup({
      spec = {
        -- import your plugins
        { import = "plugins" },
      },
      -- Configure any other settings here. See the documentation for more details.
      -- colorscheme that will be used when installing plugins.
      install = { colorscheme = { "habamax" } },
      -- automatically check for plugin updates
      checker = { enabled = true },
    })
    ```
- `./lua/plugins/avante.lua`: [avente.nvim spec](https://github.com/yetone/avante.nvim/tree/e89eb79abf5754645e20aa6074da10ed20bba33c?tab=readme-ov-file#installation)
    ```lua
    return
    {
      "yetone/avante.nvim",
      -- if you want to build from source then do `make BUILD_FROM_SOURCE=true`
      -- ⚠️ must add this setting! ! !
      build = vim.fn.has("win32") ~= 0
          and "powershell -ExecutionPolicy Bypass -File Build.ps1 -BuildFromSource false"
          or "make",
      event = "VeryLazy",
      version = false, -- Never set this value to "*"! Never!
      ---@module 'avante'
      ---@type avante.Config
      opts = {
        -- add any opts here
        -- this file can contain specific instructions for your project
        instructions_file = "avante.md",
        -- for example
        provider = "claude",
        providers = {
          claude = {
            endpoint = "https://api.anthropic.com",
            model = "claude-sonnet-4-20250514",
            timeout = 30000, -- Timeout in milliseconds
              extra_request_body = {
                temperature = 0.75,
                max_tokens = 20480,
              },
          },
          moonshot = {
            endpoint = "https://api.moonshot.ai/v1",
            model = "kimi-k2-0711-preview",
            timeout = 30000, -- Timeout in milliseconds
            extra_request_body = {
              temperature = 0.75,
              max_tokens = 32768,
            },
          },
        },
      },
      dependencies = {
        "nvim-lua/plenary.nvim",
        "MunifTanjim/nui.nvim",
        --- The below dependencies are optional,
        "nvim-mini/mini.pick", -- for file_selector provider mini.pick
        "nvim-telescope/telescope.nvim", -- for file_selector provider telescope
        "hrsh7th/nvim-cmp", -- autocompletion for avante commands and mentions
        "ibhagwan/fzf-lua", -- for file_selector provider fzf
        "stevearc/dressing.nvim", -- for input provider dressing
        "folke/snacks.nvim", -- for input provider snacks
        "nvim-tree/nvim-web-devicons", -- or echasnovski/mini.icons
        "zbirenbaum/copilot.lua", -- for providers='copilot'
        {
          -- support for image pasting
          "HakonHarnes/img-clip.nvim",
          event = "VeryLazy",
          opts = {
            -- recommended settings
            default = {
              embed_image_as_base64 = false,
              prompt_for_file_name = false,
              drag_and_drop = {
                insert_mode = true,
              },
              -- required for Windows users
              use_absolute_path = true,
            },
          },
        },
        {
          -- Make sure to set this up properly if you have lazy=true
          'MeanderingProgrammer/render-markdown.nvim',
          opts = {
            file_types = { "markdown", "Avante" },
          },
          ft = { "markdown", "Avante" },
        },
      },
    }
    ```

Following `avante.md` was manually created, which closely resembles the [example avante.md](https://github.com/yetone/avante.nvim/tree/e89eb79abf5754645e20aa6074da10ed20bba33c?tab=readme-ov-file#project-instructions-with-avantemd):

```markdown
# project instructions for VibeVim

## your role

You are an expert specializing in Lua, Neovim API, Neovim plugins, and Neovim configuration development in Lua. You understand bleeding-edge Neovim plugins and configuration paradigm.

## your mission

help build a Neovim configuration by:

- writing a Neovim configuration in Lua
- researching and adding Neovim plugins

## project context

VibeVim is a Neovim configuration specializing in AI-driven development (vibe coding). We prioritize modern design and UI, ease of usage, and modular configuration.

## technology stack

- Lua for Neovim configuration development
- Lazy.nvim: plugin manager
- avante.nvim: main AI coding assistant

## coding standards

- prefer modular configuration for each plugins and configuration
- write self-documenting code with clear variable names
- add Luadoc comment for complex functions
- use double quotes for strings, unless the string contains double quotes
```

I used Claude Pro subscription

