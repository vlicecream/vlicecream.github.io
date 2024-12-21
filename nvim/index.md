# nvim 配置


# 配置nvim

## centos7

```cpp
这里说明 这里只配置了 ccls 因为golang的编译特性 其实什么平台写go都一样
/*
    更新yum库
        yum update

    centos7 python 默认为 3.6.8 升级为 3.8.11
        yum install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gcc make
        yum -y install epel-release
        yum -y install zlib zlib
        yum -y install zlib zlib-devel

        wget http://npm.taobao.org/mirrors/python/3.8.12/Python-3.8.12.tar.xz
        xz -d Python-3.8.12.tar.xz
        tar -xf Python-3.8.12.tar
    cd Python-3.8.12
    ./configure prefix=/usr/local/python3
    make && make install

    mv /usr/bin/python /usr/bin/python.bak
        ln -s /usr/local/python3/bin/python3.8 /usr/bin/python
        python --version

        vi /usr/bin/yum
            把#! /usr/bin/python修改为#! /usr/bin/python2
        vi /usr/libexec/urlgrabber-ext-down
            把#! /usr/bin/python 修改为#! /usr/bin/python2
        为何这样配是因为yum需要使用python2才能正常运行,而之前的python指向python3 ,版本不对应,所以将其改成python2

        cd /usr/bin
        ll pip*

        rm pip3
        ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3

        pip3 install -U setuptools
        (有啥问题 可以看看 别人博客 https://www.cnblogs.com/yahoon/p/16172517.html)

    下载nodejs
        curl -sL install-node.now.sh/lts | bash

    下载nvim
        curl -o /usr/local/bin/nvim -LO https://github.com/neovim/neovim/releases/download/stable/nvim.appimage
        chmod u+x /usr/local/bin/nvim
        cp /usr/local/bin/nvim /usr/bin/nvim

    安装插件管理器 使用新的 vim-plug
        sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'

    创建初始化 neovim 配置文件
    mkdir ~/.config/nvim/
    nvim ~/.config/nvim/init.vim

  下载 nodejs & npm
    wget https://nodejs.org/dist/v16.15.1/node-v16.15.1-linux-x64.tar.xz
    tar -xvf node-v16.15.1-linux-x64.tar.xz

  coc插件 需要的步骤
      npm install neovim
      pip3 install neovim
    下载 ccls
        sudo yum install epel-release
            sudo yum install snapd
            sudo systemctl enable --now snapd.socket
            sudo ln -s /var/lib/snapd/snap /snap
            sudo snap install ccls --classic
            vim ~/.bash_profile
                export PATH="$PATH:/snap/bin/"

  安装wget(如果没有wget)
      yum -y install gcc make gcc-c++ openssl-devel wget

    使用 clash vpn 防止nvim下载插件失败
        wget -c https://github.com/Dreamacro/clash/releases/download/v1.11.8/clash-linux-amd64-v1.11.8.gz
        gzip -d ./clash-linux-armv6-v1.10.6.gz
        chmod u+x ./clash-linux-armv6-v1.10.6.gz
        处理 配置文件
            wget -O config.yaml <配置文件的url>
        启动clash
            ./clash-linux-armv6-v1.10.6.gz
        启动 http 代理
            export http_proxy=http://127.0.0.1:7890
            export https_proxy=http://127.0.0.1:7890
        验证是否ok
            curl https://www.google.com.hk/

    下载插件 
        nvim ~/.config/nvim/init.vim
        :PlugInstall

    检查健康
        :checkhealth

    修复err
        一般都会是coc出现问题
        如果是 build/index.js
            cd ~/.vim/plugged/coc.nvim
            git checkout release
*/
```

## Mac

```cpp
/*
    mac 自带的python是 3.8 版本 可以跳过

    安装nvim
        brew install neovim

    安装vim-plug
        sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
        如果 443 可以通过 /ect/hosts 解决 具体的可以百度

    创建初始化 neovim 配置文件
    mkdir ~/.config/nvim/
    nvim ~/.config/nvim/init.vim

  下载node.js
      brew install nodejs

  coc插件 需要的步骤
      npm install neovim
      pip3 install neovim
    brew install ccls

  下载插件 
        nvim ~/.config/nvim/init.vim
        :PlugInstall

    如果timeout请开启 vpn 并配置gitconfig

    检查健康
        :checkhealth

    解决 checkhealth 错误后
        nvim test.go
        :GoInstallB...

    然后如果不行就重新启动命令行 随后即可
*/
```

## Windows

```cpp
// 未完待续
```

## clash配置文件功能详解

```cpp
# HTTP 代理端口
port: 7890 

# SOCKS5 代理端口
socks-port: 7891 

# Linux 和 macOS 的 redir 代理端口
redir-port: 7892 

# 允许局域网的连接
allow-lan: true

# 规则模式：Rule（规则） / Global（全局代理）/ Direct（全局直连）
mode: rule

# 设置日志输出级别 (默认级别：silent，即不输出任何内容，以避免因日志内容过大而导致程序内存溢出）。
# 5 个级别：silent / info / warning / error / debug。级别越高日志输出量越大，越倾向于调试，若需要请自行开启。
log-level: silent

# Clash 的 RESTful API
external-controller: '0.0.0.0:9090'

# RESTful API 的口令
secret: '' 

# 您可以将静态网页资源（如 clash-dashboard）放置在一个目录中，clash 将会服务于 `RESTful API/ui`
# 参数应填写配置目录的相对路径或绝对路径。
# external-ui: folder
```

## Init.vim

```cpp
" ==================== Editor behavior ====================
" http://blog.guorongfei.com/2015/09/03/vim-shortcut/
set nu 
syntax on
set foldmethod=marker
set tabstop=2
set shiftwidth=2
set softtabstop=2
set showmode
set showcmd
set mouse=a
set encoding=utf-8
set t_Co=256
set autoindent
set cursorline
set linebreak
set showmatch
set hlsearch
set incsearch
"设置vim可以使用back键"
set backspace=2
nnoremap <C-l> :tabn<CR> "移动到下一个标签"
nnoremap <C-h> :tabp<CR> "移动到上一个标签"

au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif
 inoremap ' ''<ESC>i
 inoremap " ""<ESC>i
 inoremap ( ()<ESC>i
 inoremap [ []<ESC>i
 inoremap { {}<ESC>i
 "设置跳出自动补全的括号
 func SkipPair()
      if getline('.')[col('.') - 1] == '<' || getline('.')[col('.') - 1] == ')' || getline('.')[col('.') - 1    ] == ']' || getline('.')[col('.') - 1] == '"' || getline('.')[col('.') - 1] == "'" || getline('.')[col('.'    ) - 1] == '}'
          return "\<ESC>la"
      else
          return "\t"
      endif
  endfunc

" ===================== Install Plugins with Vim-Plug =================
call plug#begin('~/.vim/plugged')

" style
" Plug 'crusoexia/vim-monokai'
Plug 'shaunsingh/nord.nvim'

" Go
Plug 'fatih/vim-go' , { 'for': ['go', 'vim-plug'], 'tag': '*' }

" Python
Plug 'Vimjas/vim-python-pep8-indent', { 'for' :['python', 'vim-plug'] }
Plug 'numirias/semshi', { 'do': ':UpdateRemotePlugins', 'for' :['python', 'vim-plug'] }
Plug 'tweekmonster/braceless.vim', { 'for' :['python', 'vim-plug'] }

" Visual indentation 
Plug 'Yggdroot/indentLine'

" vim-monokai
Plug 'crusoexia/vim-monokai'


" annotation
Plug 'scrooloose/nerdcommenter'

" Nested parentheses are highlighted
Plug 'luochen1990/rainbow'

" Directory management plug-in
Plug 'preservim/nerdtree'
Plug 'Xuyuanp/nerdtree-git-plugin'
Plug 'ryanoasis/vim-devicons'

" Coc.nvim
Plug 'neoclide/coc.nvim', {'branch': 'release'}

" dashboard
Plug 'glepnir/dashboard-nvim'

" lualine
Plug 'nvim-lualine/lualine.nvim'

" barbar
Plug 'kyazdani42/nvim-web-devicons'
Plug 'romgrk/barbar.nvim'

" diffview
Plug 'nvim-lua/plenary.nvim'
Plug 'sindrets/diffview.nvim'

" telescope
Plug 'nvim-lua/plenary.nvim'
Plug 'nvim-telescope/telescope.nvim', { 'tag': '0.1.0' }
Plug 'nvim-treesitter/nvim-treesitter', {'do': ':TSUpdate'}

" lazygit
Plug 'kdheepak/lazygit.nvim'

" delve
Plug 'sebdah/vim-delve'

call plug#end()

" ==================== lazygit ========================
" https://github.com/kdheepak/lazygit.nvim
nnoremap <silent> <leader>gg :LazyGit<CR>

" ==================== telescope ======================
" https://github.com/nvim-telescope/telescope.nvim
" Find files using Telescope command-line sugar.
nnoremap <leader>ff <cmd>Telescope find_files<cr>
nnoremap <leader>fg <cmd>Telescope live_grep<cr>
nnoremap <leader>fb <cmd>Telescope buffers<cr>
nnoremap <leader>fh <cmd>Telescope help_tags<cr>

" Using Lua functions
nnoremap <leader>ff <cmd>lua require('telescope.builtin').find_files()<cr>
nnoremap <leader>fg <cmd>lua require('telescope.builtin').live_grep()<cr>
nnoremap <leader>fb <cmd>lua require('telescope.builtin').buffers()<cr>
nnoremap <leader>fh <cmd>lua require('telescope.builtin').help_tags()<cr>

" ==================== diffview =======================
" https://github.com/sindrets/diffview.nvim

" ==================== barbar ========================
" Move to previous/next
nnoremap <silent>    <A-,> <Cmd>BufferPrevious<CR>
nnoremap <silent>    <A-.> <Cmd>BufferNext<CR>
" Re-order to previous/next
nnoremap <silent>    <A-<> <Cmd>BufferMovePrevious<CR>
nnoremap <silent>    <A->> <Cmd>BufferMoveNext<CR>
" Goto buffer in position...
nnoremap <silent>    <A-1> <Cmd>BufferGoto 1<CR>
nnoremap <silent>    <A-2> <Cmd>BufferGoto 2<CR>
nnoremap <silent>    <A-3> <Cmd>BufferGoto 3<CR>
nnoremap <silent>    <A-4> <Cmd>BufferGoto 4<CR>
nnoremap <silent>    <A-5> <Cmd>BufferGoto 5<CR>
nnoremap <silent>    <A-6> <Cmd>BufferGoto 6<CR>
nnoremap <silent>    <A-7> <Cmd>BufferGoto 7<CR>
nnoremap <silent>    <A-8> <Cmd>BufferGoto 8<CR>
nnoremap <silent>    <A-9> <Cmd>BufferGoto 9<CR>
nnoremap <silent>    <A-0> <Cmd>BufferLast<CR>
" Pin/unpin buffer
nnoremap <silent>    <A-p> <Cmd>BufferPin<CR>
" Close buffer
nnoremap <silent>    <A-c> <Cmd>BufferClose<CR>
" Wipeout buffer
"                          :BufferWipeout
" Close commands
"                          :BufferCloseAllButCurrent
"                          :BufferCloseAllButPinned
"                          :BufferCloseAllButCurrentOrPinned
"                          :BufferCloseBuffersLeft
"                          :BufferCloseBuffersRight
" Magic buffer-picking mode
nnoremap <silent> <C-p>    <Cmd>BufferPick<CR>
" Sort automatically by...
nnoremap <silent> <Space>bb <Cmd>BufferOrderByBufferNumber<CR>
nnoremap <silent> <Space>bd <Cmd>BufferOrderByDirectory<CR>
nnoremap <silent> <Space>bl <Cmd>BufferOrderByLanguage<CR>
nnoremap <silent> <Space>bw <Cmd>BufferOrderByWindowNumber<CR>

" Other:
" :BarbarEnable - enables barbar (enabled by default)
" :BarbarDisable - very bad command, should never be used

" NOTE: If barbar's option dict isn't created yet, create it
let bufferline = get(g:, 'bufferline', {})

" Enable/disable animations
let bufferline.animation = v:true

" Enable/disable auto-hiding the tab bar when there is a single buffer
let bufferline.auto_hide = v:false

" Enable/disable current/total tabpages indicator (top right corner)
let bufferline.tabpages = v:true

" Enable/disable close button
let bufferline.closable = v:true

" Enables/disable clickable tabs
"  - left-click: go to buffer
"  - middle-click: delete buffer
let bufferline.clickable = v:true

" Excludes buffers from the tabline
let bufferline.exclude_ft = ['javascript']
let bufferline.exclude_name = ['package.json']

" Enable/disable icons
" if set to 'buffer_number', will show buffer number in the tabline
" if set to 'numbers', will show buffer index in the tabline
" if set to 'both', will show buffer index and icons in the tabline
" if set to 'buffer_number_with_icon', will show buffer number and icons in the tabline
let bufferline.icons = v:true

" Sets the icon's highlight group.
" If false, will use nvim-web-devicons colors
let bufferline.icon_custom_colors = v:false

" Configure icons on the bufferline.
let bufferline.icon_separator_active = '▎'
let bufferline.icon_separator_inactive = '▎'
let bufferline.icon_close_tab = ''
let bufferline.icon_close_tab_modified = '●'
let bufferline.icon_pinned = '車'

" If true, new buffers will be inserted at the start/end of the list.
" Default is to insert after current buffer.
let bufferline.insert_at_start = v:false
let bufferline.insert_at_end = v:false

" Sets the maximum padding width with which to surround each tab.
let bufferline.maximum_padding = 4

" Sets the maximum buffer name length.
let bufferline.maximum_length = 30

" If set, the letters for each buffer in buffer-pick mode will be
" assigned based on their name. Otherwise or in case all letters are
" already assigned, the behavior is to assign letters in order of
" usability (see order below)
let bufferline.semantic_letters = v:true

" New buffer letters are assigned in this order. This order is
" optimal for the qwerty keyboard layout but might need adjustement
" for other layouts.
let bufferline.letters =
  \ 'asdfjkl;ghnmxcvbziowerutyqpASDFJKLGHNMXCVBZIOWERUTYQP'

" Sets the name of unnamed buffers. By default format is "[Buffer X]"
" where X is the buffer number. But only a static string is accepted here.
let bufferline.no_name_title = v:null

" ==================== lualine ======================= 
lua << END
-- Eviline config for lualine
-- Author: shadmansaleh
-- Credit: glepnir
local lualine = require('lualine')

-- Color table for highlights
-- stylua: ignore
local colors = {
  bg       = '#202328',
  fg       = '#bbc2cf',
  yellow   = '#ECBE7B',
  cyan     = '#008080',
  darkblue = '#081633',
  green    = '#98be65',
  orange   = '#FF8800',
  violet   = '#a9a1e1',
  magenta  = '#c678dd',
  blue     = '#51afef',
  red      = '#ec5f67',
}

local conditions = {
  buffer_not_empty = function()
    return vim.fn.empty(vim.fn.expand('%:t')) ~= 1
  end,
  hide_in_width = function()
    return vim.fn.winwidth(0) > 80
  end,
  check_git_workspace = function()
    local filepath = vim.fn.expand('%:p:h')
    local gitdir = vim.fn.finddir('.git', filepath .. ';')
    return gitdir and #gitdir > 0 and #gitdir < #filepath
  end,
}

-- Config
local config = {
  options = {
    -- Disable sections and component separators
    component_separators = '',
    section_separators = '',
    theme = {
      -- We are going to use lualine_c an lualine_x as left and
      -- right section. Both are highlighted by c theme .  So we
      -- are just setting default looks o statusline
      normal = { c = { fg = colors.fg, bg = colors.bg } },
      inactive = { c = { fg = colors.fg, bg = colors.bg } },
    },
  },
  sections = {
    -- these are to remove the defaults
    lualine_a = {},
    lualine_b = {},
    lualine_y = {},
    lualine_z = {},
    -- These will be filled later
    lualine_c = {},
    lualine_x = {},
  },
  inactive_sections = {
    -- these are to remove the defaults
    lualine_a = {},
    lualine_b = {},
    lualine_y = {},
    lualine_z = {},
    lualine_c = {},
    lualine_x = {},
  },
}

-- Inserts a component in lualine_c at left section
local function ins_left(component)
  table.insert(config.sections.lualine_c, component)
end

-- Inserts a component in lualine_x ot right section
local function ins_right(component)
  table.insert(config.sections.lualine_x, component)
end

ins_left {
  function()
    return '▊'
  end,
  color = { fg = colors.blue }, -- Sets highlighting of component
  padding = { left = 0, right = 1 }, -- We don't need space before this
}

ins_left {
  -- mode component
  function()
    return ''
  end,
  color = function()
    -- auto change color according to neovims mode
    local mode_color = {
      n = colors.red,
      i = colors.green,
      v = colors.blue,
      [''] = colors.blue,
      V = colors.blue,
      c = colors.magenta,
      no = colors.red,
      s = colors.orange,
      S = colors.orange,
      [''] = colors.orange,
      ic = colors.yellow,
      R = colors.violet,
      Rv = colors.violet,
      cv = colors.red,
      ce = colors.red,
      r = colors.cyan,
      rm = colors.cyan,
      ['r?'] = colors.cyan,
      ['!'] = colors.red,
      t = colors.red,
    }
    return { fg = mode_color[vim.fn.mode()] }
  end,
  padding = { right = 1 },
}

ins_left {
  -- filesize component
  'filesize',
  cond = conditions.buffer_not_empty,
}

ins_left {
  'filename',
  cond = conditions.buffer_not_empty,
  color = { fg = colors.magenta, gui = 'bold' },
}

ins_left { 'location' }

ins_left { 'progress', color = { fg = colors.fg, gui = 'bold' } }

ins_left {
  'diagnostics',
  sources = { 'nvim_diagnostic' },
  symbols = { error = ' ', warn = ' ', info = ' ' },
  diagnostics_color = {
    color_error = { fg = colors.red },
    color_warn = { fg = colors.yellow },
    color_info = { fg = colors.cyan },
  },
}

-- Insert mid section. You can make any number of sections in neovim :)
-- for lualine it's any number greater then 2
ins_left {
  function()
    return '%='
  end,
}

ins_left {
  -- Lsp server name .
  function()
    local msg = 'No Active Lsp'
    local buf_ft = vim.api.nvim_buf_get_option(0, 'filetype')
    local clients = vim.lsp.get_active_clients()
    if next(clients) == nil then
      return msg
    end
    for _, client in ipairs(clients) do
      local filetypes = client.config.filetypes
      if filetypes and vim.fn.index(filetypes, buf_ft) ~= -1 then
        return client.name
      end
    end
    return msg
  end,
  icon = ' LSP:',
  color = { fg = '#ffffff', gui = 'bold' },
}

-- Add components to right sections
ins_right {
  'o:encoding', -- option component same as &encoding in viml
  fmt = string.upper, -- I'm not sure why it's upper case either ;)
  cond = conditions.hide_in_width,
  color = { fg = colors.green, gui = 'bold' },
}

ins_right {
  'fileformat',
  fmt = string.upper,
  icons_enabled = false, -- I think icons are cool but Eviline doesn't have them. sigh
  color = { fg = colors.green, gui = 'bold' },
}

ins_right {
  'branch',
  icon = '',
  color = { fg = colors.violet, gui = 'bold' },
}

ins_right {
  'diff',
  -- Is it me or the symbol for modified us really weird
  symbols = { added = ' ', modified = '柳 ', removed = ' ' },
  diff_color = {
    added = { fg = colors.green },
    modified = { fg = colors.orange },
    removed = { fg = colors.red },
  },
  cond = conditions.hide_in_width,
}

ins_right {
  function()
    return '▊'
  end,
  color = { fg = colors.blue },
  padding = { left = 1 },
}

-- Now don't forget to initialize lualine
lualine.setup(config)
END

" ==================== monokai ====================
colorscheme nord
let g:nord_contrast = v:true
let g:nord_borders = v:false
let g:nord_disable_background = v:false
let g:nord_italic = v:false

" ==================== nerdtree ==================
" autocmd vimenter * NERDTree  "自动开启Nerdtree
let g:NERDTreeWinSize = 25 "设定 NERDTree 视窗大小
let NERDTreeShowBookmarks=1  " 开启Nerdtree时自动显示Bookmarks
"打开vim时如果没有文件自动打开NERDTree
" autocmd vimenter * if !argc()|NERDTree|endif
"当NERDTree为剩下的唯一窗口时自动关闭
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif
" 设置树的显示图标
let g:NERDTreeDirArrowExpandable = '+'
let g:NERDTreeDirArrowCollapsible = '-'
let NERDTreeIgnore = ['\.pyc$']  " 过滤所有.pyc文件不显示
let g:NERDTreeShowLineNumbers=0 " 是否显示行号
let g:NERDTreeHidden=0     "不显示隐藏文件
""Making it prettier
let NERDTreeMinimalUI = 1
let NERDTreeDirArrows = 1
nnoremap <F3> :NERDTreeToggle<CR> " 开启/关闭nerdtree快捷键

" ==================== vim-go ====================
let g:go_echo_go_info = 0
let g:go_doc_popup_window = 1
let g:go_def_mapping_enabled = 0
let g:go_template_autocreate = 0
let g:go_textobj_enabled = 0
let g:go_auto_type_info = 1
let g:go_def_mapping_enabled = 0
let g:go_highlight_array_whitespace_error = 1
let g:go_highlight_build_constraints = 1
let g:go_highlight_chan_whitespace_error = 1
let g:go_highlight_extra_types = 1
let g:go_highlight_fields = 1
let g:go_highlight_format_strings = 1
let g:go_highlight_function_calls = 1
let g:go_highlight_function_parameters = 1
let g:go_highlight_functions = 1
let g:go_highlight_generate_tags = 1
let g:go_highlight_methods = 1
let g:go_highlight_operators = 1
let g:go_highlight_space_tab_error = 1
let g:go_highlight_string_spellcheck = 1
let g:go_highlight_structs = 1
let g:go_highlight_trailing_whitespace_error = 1
let g:go_highlight_types = 1
let g:go_highlight_variable_assignments = 0
let g:go_highlight_variable_declarations = 0
let g:go_doc_keywordprg_enabled = 0

" ==================== coc.nvim ====================
let g:coc_global_extensions = [
    \ 'coc-diagnostic',
    \ 'coc-docker',
    \ 'coc-eslint',
    \ 'coc-explorer',
    \ 'coc-flutter-tools',
    \ 'coc-gitignore',
    \ 'coc-html',
    \ 'coc-import-cost',
    \ 'coc-java',
    \ 'coc-jest',
    \ 'coc-json',
    \ 'coc-lists',
    \ 'coc-omnisharp',
    \ 'coc-prettier',
    \ 'coc-prisma',
    \ 'coc-pyright',
    \ 'coc-snippets',
    \ 'coc-sourcekit',
    \ 'coc-stylelint',
    \ 'coc-syntax',
    \ 'coc-tasks',
    \ 'coc-translator',
    \ 'coc-tsserver',
    \ 'coc-vetur',
    \ 'coc-vimlsp',
    \ 'coc-yaml',
    \ 'coc-yank']
inoremap <silent><expr> <TAB>
    \ pumvisible() ? "\<C-n>" :
    \ <SID>check_back_space() ? "\<TAB>" :
    \ coc#refresh()
inoremap <expr><S-TAB> pumvisible() ? "\<C-p>" : "\<C-h>"
inoremap <expr> <cr> complete_info()["selected"] != "-1" ? "\<C-y>" : "\<C-g>u\<CR>"
function! s:check_back_space() abort
    let col = col('.') - 1
    return !col || getline('.')[col - 1]  =~# '\s'
endfunction
inoremap <silent><expr> <c-space> coc#refresh()
inoremap <silent><expr> <c-o> coc#refresh()
function! Show_documentation()
    call CocActionAsync('highlight')
    if (index(['vim','help'], &filetype) >= 0)
        execute 'h '.expand('<cword>')
    else
        call CocAction('doHover')
    endif
endfunction
nnoremap <LEADER>h :call Show_documentation()<CR>
" set runtimepath^=~/.config/nvim/coc-extensions/coc-flutter-tools/
" let g:coc_node_args = ['--nolazy', '--inspect-brk=6045']
" let $NVIM_COC_LOG_LEVEL = 'debug'
" let $NVIM_COC_LOG_FILE = '/Users/david/Desktop/log.txt'

nnoremap <silent><nowait> <LEADER>d :CocList diagnostics<cr>
nmap <silent> <LEADER>- <Plug>(coc-diagnostic-prev)
nmap <silent> <LEADER>= <Plug>(coc-diagnostic-next)
nnoremap <c-c> :CocCommand<CR>
" Text Objects
xmap kf <Plug>(coc-funcobj-i)
xmap af <Plug>(coc-funcobj-a)
omap kf <Plug>(coc-funcobj-i)
omap af <Plug>(coc-funcobj-a)
xmap kc <Plug>(coc-classobj-i)
omap kc <Plug>(coc-classobj-i)
xmap ac <Plug>(coc-classobj-a)
omap ac <Plug>(coc-classobj-a)
" Useful commands
nnoremap <silent> <space>y :<C-u>CocList -A --normal yank<cr>
nmap <silent> gd <Plug>(coc-definition)
nmap <silent> gD :tab sp<CR><Plug>(coc-definition)
nmap <silent> gy <Plug>(coc-type-definition)
" nmap <silent> gi <Plug>(coc-implementation)
nmap <silent> gr <Plug>(coc-references)
nmap <leader>rn <Plug>(coc-rename)
nmap tt :CocCommand explorer<CR>
" coc-translator
nmap ts <Plug>(coc-translator-p)
" Remap for do codeAction of selected region
function! s:cocActionsOpenFromSelected(type) abort
  execute 'CocCommand actions.open ' . a:type
endfunction
xmap <leader>a  <Plug>(coc-codeaction-selected)
nmap <leader>aw  <Plug>(coc-codeaction-selected)w
" coctodolist
" nnoremap <leader>tn :CocCommand todolist.create<CR>
" nnoremap <leader>tl :CocList todolist<CR>
" nnoremap <leader>tu :CocCommand todolist.download<CR>:CocCommand todolist.upload<CR>
" coc-tasks
noremap <silent> <leader>ts :CocList tasks<CR>
" coc-snippets
imap <C-l> <Plug>(coc-snippets-expand)
vmap <C-e> <Plug>(coc-snippets-select)
let g:coc_snippet_next = '<c-e>'
let g:coc_snippet_prev = '<c-n>'
imap <C-e> <Plug>(coc-snippets-expand-jump)
autocmd BufRead,BufNewFile tsconfig.json set filetype=jsonc

" ==================== indentLine ====================
let g:indent_guides_guide_size            = 1  
let g:indent_guides_start_level           = 2  





" ==================== nerdcommenter ====================
"add spaces after comment delimiters by default
let g:NERDSpaceDelims = 1
" python 自动的会多加一个空格
au FileType python let g:NERDSpaceDelims = 0

" Use compact syntax for prettified multi-line comments
let g:NERDCompactSexyComs = 1

" Align line-wise comment delimiters flush left instead of following code indentation
let g:NERDDefaultAlign = 'left'

" Set a language to use its alternate delimiters by default
let g:NERDAltDelims_java = 1

" Add your own custom formats or override the defaults
" let g:NERDCustomDelimiters = { 'c': { 'left': '/**','right': '*/' } }

" Allow commenting and inverting empty lines (useful when commenting a region)
let g:NERDCommentEmptyLines = 1

" Enable trimming of trailing whitespace when uncommenting
let g:NERDTrimTrailingWhitespace = 1

" Enable NERDCommenterToggle to check all selected lines is commented or not
let g:NERDToggleCheckAllLines = 1

" ==================== rainbow ====================
let g:rainbow_active = 1
let g:rainbow_conf = {
\   'guifgs': ['darkorange3', 'seagreen3', 'royalblue3', 'firebrick'],
\   'ctermfgs': ['lightyellow', 'lightcyan','lightblue', 'lightmagenta'],
\   'operators': '_,_',
\   'parentheses': ['start=/(/ end=/)/ fold', 'start=/\[/ end=/\]/ fold', 'start=/{/ end=/}/ fold'],
\   'separately': {
\       '*': {},
\       'tex': {
\           'parentheses': ['start=/(/ end=/)/', 'start=/\[/ end=/\]/'],
\       },
\       'lisp': {
\           'guifgs': ['darkorange3', 'seagreen3', 'royalblue3', 'firebrick'],
\       },
\       'vim': {
\           'parentheses': ['start=/(/ end=/)/', 'start=/\[/ end=/\]/', 'start=/{/ end=/}/ fold', 'start=/(/ end=/)/ containedin=vimFuncBody', 'start=/\[/ end=/\]/ containedin=vimFuncBody', 'start=/{/ end=/}/ fold containedin=vimFuncBody'],
\       },
\       'html': {
\           'parentheses': ['start=/\v\<((area|base|br|col|embed|hr|img|input|keygen|link|menuitem|meta|param|source|track|wbr)[ >])@!\z([-_:a-zA-Z0-9]+)(\s+[-_:a-zA-Z0-9]+(\=("[^"]*"|'."'".'[^'."'".']*'."'".'|[^ '."'".'"><=`]*))?)*\>/ end=#</\z1># fold'],
\       },
\       'css': 0,
\   }
\}
```

## coc-settings.json

```json
{
    "coc.preferences.semanticTokensHighlights": false,
    "coc.preferences.enableFloatHighlight": true,
    "coc.preferences.snippetStatusText": "Ⓢ ",
    "coc.preferences.extensionUpdateCheck": "daily",
    "coc.preferences.messageLevel": "error",
    "coc.source.around.firstMatch": false,
    "coc.source.buffer.firstMatch": false,
    "coc.source.syntax.firstMatch": false,
    "suggest.detailMaxLength": 60,
    "suggest.noselect": true,
    "suggest.enablePreselect": false,
    "suggest.triggerAfterInsertEnter": true,
    "suggest.autoTrigger": "always",
    "suggest.timeout": 5000,
    "suggest.enablePreview": true,
    "suggest.floatEnable": true,
    "suggest.detailField": "preview",
    "suggest.snippetIndicator": "",
    "suggest.triggerCompletionWait": 100,
    "suggest.echodocSupport": true,
    "suggst.completionItemKindLabels": {
        "class": "\uf0e8",
        "color": "\ue22b",
        "constant": "\uf8fe",
        "default": "\uf29c",
        "enum": "\uf435",
        "enumMember": "\uf02b",
        "event": "\ufacd",
        "field": "\uf93d",
        "file": "\uf723",
        "folder": "\uf115",
        "function": "\u0192",
        "interface": "\uf417",
        "keyword": "\uf1de",
        "method": "\uf6a6",
        "module": "\uf40d",
        "operator": "\uf915",
        "property": "\ue624",
        "reference": "\ufa46",
        "snippet": "\ue60b",
        "struct": "\ufb44",
        "text": "\ue612",
        "typeParameter": "\uf728",
        "unit": "\uf475",
        "value": "\uf89f",
        "variable": "\ue71b"
    },
    "diagnostic.signOffset": 2,
    "diagnostic.errorSign": "!",
    "diagnostic.warningSign": "!!",
    "diagnostic.infoSign": "\uf129",
    "diagnostic.hintSign": "\uf864",
    "diagnostic.displayByAle": false,
    "diagnostic.refreshOnInsertMode": false,
    "diagnostic.checkCurrentLine": true,
    "diagnostic.virtualTextPrefix": " ❯❯❯ ",
    "diagnostic.virtualText": false,
    "codeLens.enable": true,
    "list.previewHighlightGroup": "Statement",
    "list.nextKeymap": "<C-e>",
    "list.previousKeymap": "<C-u>",
    "importCost.bundleSizeDecoration": "both",
    "importCost.typescriptExtensions": ["\\.tsx?$"],
    "importCost.javascriptExtensions": ["\\.jsx?$"],
    "importCost.showCalculatingDecoration": true,
    "importCost.debug": false,
    "snippets.ultisnips.directories": [
        "$HOME/.config/nvim/Ultisnips/",
        "$HOME/.config/nvim/plugged/vim-snippets/UltiSnips/"
    ],
    "coc.preferences.formatOnSaveFiletypes": [
        "javascript",
        "typescript",
        "typescriptreact",
        "html",
        "css",
        "json",
        "jsonc",
        "java",
        "python",
        "vue",
        "svelte",
        "cs",
        "racket",
        "prisma"
    ],
    "yaml.format.enable": true,
    "signature.target": "float",
    "yank.enableCompletion": false,
    "typescript.suggestionActions.enabled": true,
    "typescript.format.enabled": true,
    "jest.watch": false,
    "explorer.width": 38,
    "explorer.quitOnOpen": true,
    "explorer.sources": [
        {
            "name": "buffer",
            "expand": false
        },
        {
            "name": "file",
            "expand": true
        }
    ],
    "explorer.file.column.indent.indentLine": true,
    "explorer.file.showHiddenFiles": true,
    "explorer.icon.enableNerdfont": true,
    "explorer.file.column.git.showIgnored": true,
    "explorer.keyMappingMode": "none",
    "explorer.buffer.showHiddenBuffers": false,
    "explorer.keyMappings.global": {
        "u": "nodePrev",
        "e": "nodeNext",
        "h": "toggleSelection",
        "<tab>": "actionMenu",
        "gl": "expandRecursive",
        "gh": "collapseRecursive",
        "i": ["wait", "expandable?", "expand", "open"],
        "<cr>": ["wait", "expandable?", "cd", "open"],
        "I": "open:vsplit",
        "o": ["wait", "expanded?", "collapse", "expand"],
        "O": "open:tab",
        "n": "collapse",
        "l": "gotoParent",
        "yp": "copyFilepath",
        "yn": "copyFilename",
        "yy": "copyFile",
        "dd": "cutFile",
        "pp": "pasteFile",
        "dD": "deleteForever",
        "a": "addFile",
        "k": "addFile",
        "M": "addDirectory",
        "cw": "rename",
        ".": "toggleHidden",
        "zh": "toggleHidden",
        "R": "refresh",
        "?": "help",
        "q": "quit",
        "X": "systemExecute",
        "gd": "listDrive",
        "f": "search",
        "F": "searchRecursive",
        "B": "gotoSource:file",
        "b": "gotoSource:buffer",
        "[[": "sourcePrev",
        "]]": "sourceNext",
        "[d": "diagnosticPrev",
        "]d": "diagnosticNext",
        "[c": "gitPrev",
        "]c": "gitNext",
        "<<": "gitStage",
        ">>": "gitUnstage"
    },
    "flutter.outlineWidth": 40,
    "flutter.outlineIconPadding": 0,
    "flutter.UIPath": true,
    "flutter.openDevLogSplitCommand": "botright 12split",
    "flutter.lsp.initialization.onlyAnalyzeProjectsWithOpenFiles": false,
    "flutter.trace.server": "off",
    "tslint.autoFixOnSave": false,
    "python.autoComplete.addBrackets": true,
    "python.jediEnabled": false,
    "python.formatting.provider": "yapf",
    "python.formatting.yapfArgs": [
        "--style",
        "{SPACES_AROUND_POWER_OPERATOR: True, SPACES_BEFORE_COMMENT: 1}"
    ],
    "html.format.enable": true,
    "javascript.referencesCodeLens.enable": true,
    "javascript.showUnused": true,
    "javascript.suggest.names": true,
    "javascript.suggestionActions.enabled": true,
    "json.format.enable": true,
    "eslint.autoFixOnSave": false,
    "prettier.printWidth": 100,
    "prettier.disableLanguages": [],
    "prettier.formatterPriority": 1,
    "prettier.useTabs": true,
    "prettier.trailingComma": "all",
    "prettier.singleQuote": false,
    "todolist.autoUpload": true,
    "todolist.promptForReminder": false,
    "coc-actions.hideCursor": false,
    "coc-actions.showActionKind": true,
    "diagnostic-languageserver.filetypes": {
        "vim": "vint",
        "email": "languagetool",
        "markdown": ["write-good", "markdownlint"],
        "sh": "shellcheck",
        "elixir": ["mix_credo", "mix_credo_compile"],
        "eelixir": ["mix_credo", "mix_credo_compile"],
        "php": ["phpstan", "psalm"]
    },
    "diagnostic-languageserver.formatFiletypes": {
        "elixir": "mix_format",
        "eelixir": "mix_format"
    },
    "languageserver": {
        "lua": {
            "command": "lua-lsp",
            "filetypes": ["lua"]
        },
        "swift": {
            "command": "/Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/sourcekit-lsp",
            "args": [],
            "filetypes": ["swift"],
            "initializationOptions": {},
            "settings": {}
        },
        "golang": {
            "command": "gopls",
            "rootPatterns": ["go.mod", ".vim/", ".git/", ".hg/"],
            "filetypes": ["go"],
            "initializationOptions": {
                "usePlaceholders": true
            }
        },
        "bash": {
            "command": "bash-language-server",
            "args": ["start"],
            "filetypes": ["sh"],
            "ignoredRootPaths": []
        },
        "racket": {
            "command": "racket",
            "args": ["--lib", "racket-langserver"],
            "filetypes": ["scheme", "racket"]
        },
        "ccls": {
            "command": "ccls",
            "filetypes": ["c", "cpp", "cuda", "objc", "objcpp"],
            "rootPatterns": [".ccls", ".ccls-root", "compile_commands.json", ".git/", ".hg/"],
            "initializationOptions": {
                "cache": {
                    "directory": "/tmp/ccls"
                }
            }
        }
    }
}
```

