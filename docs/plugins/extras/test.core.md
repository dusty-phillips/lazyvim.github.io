# Neotest

<!-- plugins:start -->

To use this, add it to your **lazy.nvim** imports:

```lua title="lua/config/lazy.lua" {4}
require("lazy").setup({
  spec = {
    { "LazyVim/LazyVim", import = "lazyvim.plugins" },
    { import = "lazyvim.plugins.extras.test.core" },
    { import = "plugins" },
  },
})
```

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

## [which-key.nvim](https://github.com/folke/which-key.nvim)

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = {
  defaults = {
    ["<leader>t"] = { name = "+test" },
  },
}
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "folke/which-key.nvim",
  optional = true,
  opts = {
    defaults = {
      ["<leader>t"] = { name = "+test" },
    },
  },
}
```

</TabItem>

</Tabs>

## [neotest](https://github.com/nvim-neotest/neotest)

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = {
  -- Can be a list of adapters like what neotest expects,
  -- or a table of adapter names, mapped to adapter configs.
  -- The adapter will then be automatically loaded with the config.
  adapters = {},
  -- Example for loading neotest-go with a custom config
  -- adapters = {
  --   ["neotest-go"] = {
  --     args = { "-tags=integration" },
  --   },
  -- },
}
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "nvim-neotest/neotest",
  opts = {
    -- Can be a list of adapters like what neotest expects,
    -- or a table of adapter names, mapped to adapter configs.
    -- The adapter will then be automatically loaded with the config.
    adapters = {},
    -- Example for loading neotest-go with a custom config
    -- adapters = {
    --   ["neotest-go"] = {
    --     args = { "-tags=integration" },
    --   },
    -- },
  },
  config = function(_, opts)
    local neotest_ns = vim.api.nvim_create_namespace("neotest")
    vim.diagnostic.config({
      virtual_text = {
        format = function(diagnostic)
          -- Replace newline and tab characters with space for more compact diagnostics
          local message = diagnostic.message:gsub("\n", " "):gsub("\t", " "):gsub("%s+", " "):gsub("^%s+", "")
          return message
        end,
      },
    }, neotest_ns)

    if opts.adapters then
      local adapters = {}
      for name, config in pairs(opts.adapters or {}) do
        if type(name) == "number" then
          adapters[#adapters + 1] = config
        elseif config ~= false then
          local adapter = require(name)
          if type(config) == "table" and not vim.tbl_isempty(config) then
            adapter = adapter(config)
          end
          adapters[#adapters + 1] = adapter
        end
      end
      opts.adapters = adapters
    end

    require("neotest").setup(opts)
  end,
  -- stylua: ignore
  keys = {
    { "<leader>tr", function() require("neotest").run.run() end, desc = "Run Nearest" },
    { "<leader>tR", function() require("neotest").run.run(vim.fn.expand("%")) end, desc = "Run File" },
    { "<leader>ts", function() require("neotest").summary.toggle() end, desc = "Toggle Summary" },
    { "<leader>to", function() require("neotest").output.open({ enter = true, auto_close = true }) end, desc = "Show Output" },
    { "<leader>tO", function() require("neotest").output_panel.toggle() end, desc = "Toggle Output Panel" },
    { "<leader>tS", function() require("neotest").run.stop() end, desc = "Stop" },
  },
}
```

</TabItem>

</Tabs>

## [nvim-dap](https://github.com/mfussenegger/nvim-dap)

<Tabs>

<TabItem value="opts" label="Options">

```lua
opts = nil
```

</TabItem>


<TabItem value="code" label="Full Spec">

```lua
{
  "mfussenegger/nvim-dap",
  optional = true,
  -- stylua: ignore
  keys = {
    { "<leader>td", function() require("neotest").run.run({strategy = "dap"}) end, desc = "Debug Nearest" },
  },
}
```

</TabItem>

</Tabs>

<!-- plugins:end -->