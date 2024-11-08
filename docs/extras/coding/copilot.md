# Copilot

<!-- plugins:start -->

:::info
You can enable the extra with the `:LazyExtras` command.
Plugins marked as optional will only be configured if they are installed.
:::

<details>
<summary>Alternatively, you can add it to your <code>lazy.nvim</code> imports</summary>

```lua title="lua/config/lazy.lua" {4}
require("lazy").setup({
  spec = {
    { "LazyVim/LazyVim", import = "lazyvim.plugins" },
    { import = "lazyvim.plugins.extras.coding.copilot" },
    { import = "plugins" },
  },
})
```

</details>

Below you can find a list of included plugins and their default settings.

:::caution
You don't need to copy the default settings to your config.
They are only shown here for reference.
:::

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## [copilot.lua](https://github.com/zbirenbaum/copilot.lua)

 copilot


<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = {
  suggestion = { enabled = false },
  panel = { enabled = false },
  filetypes = {
    markdown = true,
    help = true,
  },
}
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "zbirenbaum/copilot.lua",
  cmd = "Copilot",
  build = ":Copilot auth",
  opts = {
    suggestion = { enabled = false },
    panel = { enabled = false },
    filetypes = {
      markdown = true,
      help = true,
    },
  },
}
```

</TabItem>

</Tabs>

## [nvim-cmp](https://github.com/hrsh7th/nvim-cmp)

 copilot cmp source


<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = function(_, opts)
  table.insert(opts.sources, 1, {
    name = "copilot",
    group_index = 1,
    priority = 100,
  })
end
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "nvim-cmp",
  dependencies = {
    {
      "zbirenbaum/copilot-cmp",
      dependencies = "copilot.lua",
      opts = {},
      config = function(_, opts)
        local copilot_cmp = require("copilot_cmp")
        copilot_cmp.setup(opts)
        -- attach cmp source whenever copilot attaches
        -- fixes lazy-loading issues with the copilot cmp source
        LazyVim.lsp.on_attach(function(client)
          copilot_cmp._on_insert_enter({})
        end, "copilot")
      end,
    },
  },
  ---@param opts cmp.ConfigSchema
  opts = function(_, opts)
    table.insert(opts.sources, 1, {
      name = "copilot",
      group_index = 1,
      priority = 100,
    })
  end,
}
```

</TabItem>

</Tabs>

## [copilot-cmp](https://github.com/zbirenbaum/copilot-cmp)

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = {}
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "zbirenbaum/copilot-cmp",
  dependencies = "copilot.lua",
  opts = {},
  config = function(_, opts)
    local copilot_cmp = require("copilot_cmp")
    copilot_cmp.setup(opts)
    -- attach cmp source whenever copilot attaches
    -- fixes lazy-loading issues with the copilot cmp source
    LazyVim.lsp.on_attach(function(client)
      copilot_cmp._on_insert_enter({})
    end, "copilot")
  end,
}
```

</TabItem>

</Tabs>

## [copilot.lua](https://github.com/zbirenbaum/copilot.lua)

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = {
  suggestion = {
    enabled = true,
    auto_trigger = true,
    keymap = { accept = false },
  },
}
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "zbirenbaum/copilot.lua",
  event = "InsertEnter",
  opts = {
    suggestion = {
      enabled = true,
      auto_trigger = true,
      keymap = { accept = false },
    },
  },
}
```

</TabItem>

</Tabs>

## [lualine.nvim](https://github.com/nvim-lualine/lualine.nvim) _(optional)_

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = function(_, opts)
  local colors = {
    [""] = LazyVim.ui.fg("Special"),
    ["Normal"] = LazyVim.ui.fg("Special"),
    ["Warning"] = LazyVim.ui.fg("DiagnosticError"),
    ["InProgress"] = LazyVim.ui.fg("DiagnosticWarn"),
  }
  table.insert(opts.sections.lualine_x, 2, {
    function()
      local icon = LazyVim.config.icons.kinds.Copilot
      local status = require("copilot.api").status.data
      return icon .. (status.message or "")
    end,
    cond = function()
      if not package.loaded["copilot"] then
        return
      end
      local ok, clients = pcall(LazyVim.lsp.get_clients, { name = "copilot", bufnr = 0 })
      if not ok then
        return false
      end
      return ok and #clients > 0
    end,
    color = function()
      if not package.loaded["copilot"] then
        return
      end
      local status = require("copilot.api").status.data
      return colors[status.status] or colors[""]
    end,
  })
end
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "nvim-lualine/lualine.nvim",
  optional = true,
  event = "VeryLazy",
  opts = function(_, opts)
    local colors = {
      [""] = LazyVim.ui.fg("Special"),
      ["Normal"] = LazyVim.ui.fg("Special"),
      ["Warning"] = LazyVim.ui.fg("DiagnosticError"),
      ["InProgress"] = LazyVim.ui.fg("DiagnosticWarn"),
    }
    table.insert(opts.sections.lualine_x, 2, {
      function()
        local icon = LazyVim.config.icons.kinds.Copilot
        local status = require("copilot.api").status.data
        return icon .. (status.message or "")
      end,
      cond = function()
        if not package.loaded["copilot"] then
          return
        end
        local ok, clients = pcall(LazyVim.lsp.get_clients, { name = "copilot", bufnr = 0 })
        if not ok then
          return false
        end
        return ok and #clients > 0
      end,
      color = function()
        if not package.loaded["copilot"] then
          return
        end
        local status = require("copilot.api").status.data
        return colors[status.status] or colors[""]
      end,
    })
  end,
}
```

</TabItem>

</Tabs>

## [blink.cmp](https://github.com/saghen/blink.cmp) _(optional)_

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = {
  windows = {
    ghost_text = {
      enabled = false,
    },
  },
  keymap = {
    ["<Tab>"] = {
      function(cmp)
        if cmp.is_in_snippet() then
          return cmp.accept()
        elseif require("copilot.suggestion").is_visible() then
          LazyVim.create_undo()
          require("copilot.suggestion").accept()
          return true
        else
          return cmp.select_and_accept()
        end
      end,
      "snippet_forward",
      "fallback",
    },
  },
}
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "saghen/blink.cmp",
  optional = true,
  specs = {
    {
      "zbirenbaum/copilot.lua",
      event = "InsertEnter",
      opts = {
        suggestion = {
          enabled = true,
          auto_trigger = true,
          keymap = { accept = false },
        },
      },
    },
  },
  opts = {
    windows = {
      ghost_text = {
        enabled = false,
      },
    },
    keymap = {
      ["<Tab>"] = {
        function(cmp)
          if cmp.is_in_snippet() then
            return cmp.accept()
          elseif require("copilot.suggestion").is_visible() then
            LazyVim.create_undo()
            require("copilot.suggestion").accept()
            return true
          else
            return cmp.select_and_accept()
          end
        end,
        "snippet_forward",
        "fallback",
      },
    },
  },
}
```

</TabItem>

</Tabs>

<!-- plugins:end -->
