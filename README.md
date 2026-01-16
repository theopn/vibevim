# VibeVim

VibeVim is a Neovim configuration 

I have never been a fan of "vibe coding tools," but I am also aware of the prevalence of them in the software engineering industry.
If it (ever) becomes the future, I would rather

(a) be aware of its capability and how they work
(b) use Neovim rather than a VS Code-based editor

I started with a simple `init.lua` containing the recommended installation for [avante.nvim](https://github.com/yetone/avante.nvim) and let it build its own configuration from there.
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

- 2026-01-16: following `init.lua`, containing the standard [lazy.nvim bootstrap](https://lazy.folke.io/installation) and [avente.nvim spec](https://github.com/yetone/avante.nvim?tab=readme-ov-file#installation) was created.
    ```lua
    -- Bootstrap lazy.nvim
    local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
    if not (vim.uv or vim.loop).fs_stat(lazypath) then
      local lazyrepo = "https://github.com/folke/lazy.nvim.git"
      local out = vim.fn.system({ "git", "clone", "--filter=blob:none", "--branch=stable", lazyrepo, lazypath })
      if vim.v.shell_error ~= 0 then
        vim.api.nvim_echo({
          { "Failed to clone lazy.nvim:\n", "ErrorMsg" },
          { out,                            "WarningMsg" },
          { "\nPress any key to exit..." },
        }, true, {})
        vim.fn.getchar()
        os.exit(1)
      end
    end
    vim.opt.rtp:prepend(lazypath)

    require("lazy").setup({
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
          "nvim-mini/mini.pick",         -- for file_selector provider mini.pick
          "nvim-telescope/telescope.nvim", -- for file_selector provider telescope
          "hrsh7th/nvim-cmp",            -- autocompletion for avante commands and mentions
          "ibhagwan/fzf-lua",            -- for file_selector provider fzf
          "stevearc/dressing.nvim",      -- for input provider dressing
          "folke/snacks.nvim",           -- for input provider snacks
          "nvim-tree/nvim-web-devicons", -- or echasnovski/mini.icons
          "zbirenbaum/copilot.lua",      -- for providers='copilot'
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
    })
    ```


