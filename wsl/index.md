# Unreal NVIM


## ***Scoop***

*普通权限打开 PowerShell*

*为了允许下载并运行安装脚本，你需要执行以下命令：*

```shell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

*下载并安装 Scoop*

```shell
irm get.scoop.sh | iex
```

*安装到自定义目录*

```shell
# 1. 先设置环境变量，指向你想安装的路径
$env:SCOOP='D:\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')

# 2. 再执行安装命令
irm get.scoop.sh | iex
```

*验证安装*

```shell
scoop help
```

*安装extras库*

```shell
scoop update
scoop bucket add extras
```

## ***NVIM***

*安装 最新的nvim*

```
scoop update
scoop install neovim
```

*验证安装*

```
nvim --version
```

*增加系统环境变量，更换nvim默认路径*

1. *XDG_CACHE_HOME：E:\Scoop\apps\neovim*
2. *XDG_CONFIG_HOME：E:\Scoop\apps\neovim*
3. *XDG_DATA_HOME：E:\Scoop\apps\neovim*
4. *XDG_STATE_HOME：E:\Scoop\apps\neovim*

## ***Lazy.vim***

*直接输入*

```cpp
git clone https://github.com/LazyVim/starter $env:LOCALAPPDATA\nvim
```

*随后打开命令行，输入 `nvim`，就开始自动安装插件拉*

*在nvim中输入`checkhealth`，随后有Error（也就是叉号），截图问问AI把，需要补一些环境*

## ***Unreal NVIM***

*在nvim plugin下，创建 `Unreal.lua`*

```lua
return {
	-- UNL.nvim (基础核心插件)
	{
		"taku25/UNL.nvim",
		build = "cargo build --release --manifest-path scanner/Cargo.toml",
		opts = {},
	},

	-- UBT.nvim (编译工具集成)
	{
		"taku25/UBT.nvim",
		dependencies = {
			"taku25/UNL.nvim",
			"nvim-telescope/telescope.nvim",
		},

		opts = {
			presets = {
				{
					Platform = "Win64",
					Configuration = "DebugGame",
					IsEditor = true,
				},
			},
		},

		config = function(_, opts)
			-- 1. 正常初始化
			require("UBT").setup(opts)

			-- 2. 【万能拦截器】
			-- 不管是谁生成的命令，在发给系统执行前，我们在这里拦截并修正
			local success, runner = pcall(require, "UBT.job.runner")
			if success then
				local original_start = runner.start
				runner.start = function(name, cmd, start_opts)
					-- 针对不同的 cmd 类型进行处理（防止红屏报错）
					local final_cmd = cmd

					if type(cmd) == "string" then
						-- 如果是字符串，强行把带路径的 Target 替换成纯名字
						-- 逻辑：匹配任何以 /SimpleBetaEditor 结尾的路径，替换为 SimpleBetaEditor
						final_cmd = cmd:gsub("[^%s\"']*/SimpleBetaEditor", "SimpleBetaEditor")
					elseif type(cmd) == "table" then
						-- 如果是表格，遍历每一个参数进行替换
						for i, v in ipairs(cmd) do
							if type(v) == "string" then
								cmd[i] = v:gsub("[^%s\"']*/SimpleBetaEditor", "SimpleBetaEditor")
							end
						end
						final_cmd = cmd
					end

					-- 在控制台打印一下，让你看到我们确实修成功了
					print("拦截修正完成，准备执行编译...")

					return original_start(name, final_cmd, start_opts)
				end
			end
		end,
	},

	-- UCM.nvim (类创建工具)
	{
		"taku25/UCM.nvim",
		dependencies = {
			"taku25/UNL.nvim",
			"nvim-telescope/telescope.nvim",
		},
		config = function()
			require("UCM").setup()
		end,
		opts = {},
	},

	-- ULG.nvim (日志查看器)
	{
		"taku25/ULG.nvim",
		dependencies = {
			"taku25/UNL.nvim",
		},
		config = function()
			require("ULG").setup()
		end,
		opts = {},
	},

	-- UEP.nvim (项目增强)
	{
		"taku25/UEP.nvim",
		dependencies = {
			"taku25/UNL.nvim",
			"nvim-lua/plenary.nvim",
			"nvim-telescope/telescope.nvim",
			"ibhagwan/fzf-lua",
		},
		opts = {
			files_extensions = { "cpp", "h", "hpp", "inl", "ini", "cs", "uproject" },
		},
		config = function(_, opts)
			require("UEP").setup(opts)
		end,
	},

	-- UNX.nvim (综合扩展)
	{
		"taku25/UNX.nvim",
		dependencies = {
			"taku25/UNL.nvim",
			"taku25/UEP.nvim",
			"MunifTanjim/nui.nvim",
			"nvim-tree/nvim-web-devicons",
			"taku25/UCM.nvim",
			"taku25/ULG.nvim",
			{
				"nvim-treesitter/nvim-treesitter",
				build = ":TSUpdate",
				dependencies = {
					"nvim-treesitter/nvim-treesitter-textobjects",
				},
				config = function()
					require("nvim-treesitter").setup({
						ensure_installed = { "cpp", "hlsl" },
						highlight = { enable = true },
					})
				end,
			},
		},
		opts = {
			window = {
				position = "left", -- "left" or "right"
				size = {
					width = 60,
				},
			},
			vcs = {
				git = { enabled = false },
				p4 = {
					enabled = true,
					auto_checkout = true, -- Automatically checkout read-only files on edit
				},
			},
		},
		config = function(_, opts)
			require("UNX").setup(opts)
		end,
	},

	-- UnrealDev.nvim
	{
		"taku25/UnrealDev.nvim",
		ft = { "cpp", "c" },
		cmd = {
			"UDEV",
		},
		dependencies = {
			"j-hui/fidget.nvim",
			"nvim-telescope/telescope.nvim",
			"taku25/UNL.nvim",
			"taku25/UEP.nvim",
			"taku25/UBT.nvim",
			"taku25/UCM.nvim",
			"taku25/USH.nvim",
			"taku25/ULG.nvim",
			{
				"taku25/USX.nvim",
				lazy = false,
			},
		},
	},

	-- USX.nvim
	{
		"taku25/USX.nvim",
		opts = {
			-- Add your settings here (details below)
		},
	},

	-- blink.cmp
	{
		"Saghen/blink.cmp",
		dependencies = {
			{ "taku25/blink-cmp-unreal" },
		},
		opts = {
			sources = {
				default = { "lsp", "buffer", "path", "unreal" },
				providers = {
					unreal = {
						module = "blink-cmp-unreal",
						name = "unreal",
						score_offset = 15,
					},
				},
			},
			--... other blink.cmp settings
		},
	},

	{
		{
			"taku25/tree-sitter-unreal-cpp",
			opts = {},
			config = function() end,
		},
		{
			"nvim-treesitter/nvim-treesitter",
			branch = "main",
			lazy = false,
			build = ":TSUpdate",
			opts = {
				--...
			},
			config = function(_, opts)
				vim.api.nvim_create_autocmd("User", {
					pattern = "TSUpdate",
					callback = function()
						require("nvim-treesitter.parsers").cpp = {
							install_info = {
								url = "https://github.com/taku25/tree-sitter-unreal-cpp",
								revision = "e2b94d3bd3ce359ff732116cc21f34ecbecfca50",
							},
						}
					end,
				})
				require("nvim-treesitter").setup(opts)
			end,
		},
	},

	{
		"taku25/UDB.nvim",
		dependencies = {
			"taku25/UNL.nvim",
			"mfussenegger/nvim-dap",
			-- Optional: Recommended for a better UI experience
			"rcarriga/nvim-dap-ui",
		},
		opts = {
			-- Place UDB specific configurations here
		},
	},
}

```


