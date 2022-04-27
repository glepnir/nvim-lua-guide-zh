# 在 neovim 中使用 Lua

[nvim-lua-guide](https://github.com/nanotee/nvim-lua-guide) 中文版简易教程

译者: Neovim Core Developer

## 目录

- [在 Neovim 中使用 Lua](#在-neovim-中使用-lua)
  - [目录](#目录)
  - [简介](#简介)
    - [学习 Lua](#学习-lua)
    - [现有的一些在 Neovim 中使用 Lua 的教程](#现有的一些在-neovim-中使用-lua-的教程)
    - [相关插件](#相关插件)
  - [Lua 文件位置](#lua-文件位置)
      - [警告](#警告)
      - [提示](#提示)
      - [包说明](#包说明)
  - [在 Vimscript 中使用 Lua](#在-vimscript-中使用-lua)
    - [:lua](#lua)
      - [警告](#警告-1)
    - [:luado](#luado)
    - [:luafile](#luafile)
      - [luafile 对比 require():](#luafile-对比-require)
    - [luaeval()](#luaeval)
    - [v:lua](#vlua)
      - [Caveats](#caveats)
  - [Vim 命名空间](#vim-命名空间)
      - [Tips](#tips)
  - [在 Lua 中使用 Vimscript](#在-lua-中使用-vimscript)
    - [vim.api.nvim_eval()](#vimapinvim_eval)
      - [Caveats](#caveats-1)
    - [vim.api.nvim_exec()](#vimapinvim_exec)
    - [vim.api.nvim_command()](#vimapinvim_command)
      - [Tips](#tips-1)
  - [管理 vim 的设置选项](#管理-vim-的设置选项)
    - [使用 api 函数](#使用-api-函数)
    - [使用元访问器](#使用元访问器)
      - [Caveats](#caveats-2)
  - [管理 vim 的内部变量](#管理-vim-的内部变量)
    - [使用 api 函数](#使用-api-函数-1)
    - [使用元访问器](#使用元访问器-1)
      - [Caveats](#caveats-3)
  - [调用 Vimscript 函数](#调用-vimscript-函数)
    - [vim.call()](#vimcall)
    - [vim.fn.{function}()](#vimfnfunction)
      - [Tips](#tips-2)
      - [Caveats](#caveats-4)
  - [定义映射](#定义映射)
  - [定义用户命令](#定义用户命令)
  - [定义自动命令](#定义自动命令)
  - [定义语法高亮](#定义语法高亮)
  - [General tips and recommendations](#general-tips-and-recommendations)
  - [Miscellaneous](#miscellaneous)
    - [vim.loop](#vimloop)
    - [vim.lsp](#vimlsp)
    - [vim.treesitter](#vimtreesitter)
    - [Transpilers](#transpilers)

## 简介

Lua 作为 Neovim 中的一等语言的集成正在成为它的杀手级特性之一。然而，学习如何用 Lua 编写插件的教程数量并不像用 Vimscript 编写插件那样多。这是一种尝试，试图提供一些基本信息，让人们可以使用 Lua 编写 Neovim 插件。

本指南假定您使用的是最新的 Neovim [Nighly build](https://github.com/neovim/neovim/releases/tag/nightly)。由于 Neovim 的 0.5 版本是开发版本，请记住，正在积极开发的一些 API 并不十分稳定，在发布之前可能会发生变化。

### 学习 Lua

不同于原版教程，以下资源适用于国内用户：
- [在 Y 分钟内学习 X 关于 Lua 的页面](https://learnxinyminutes.com/docs/lua/)
- [Lua 菜鸟教程](https://www.runoob.com/lua/lua-tutorial.html)
- [Lua 用户维基](http://lua-users.org/wiki/LuaDirectory)
- [Lua 的官方参考手册](https://www.lua.org/manual/5.1/)

Lua 是一种非常干净和简单的语言。它很容易学习，特别是如果你有其他编程语言基础的例如 TypeScript / JavaScript 等，会更加容易上手 Lua。注意：Neovim 嵌入的 Lua 版本是 LuaJIT 2.1.0，它与 Lua 5.1 保持兼容（带有几个 5.2 扩展）

### 现有的一些在 Neovim 中使用 Lua 的教程

已经编写了一些教程来帮助人们用 Lua 编写插件。他们中的一些人在写这本指南时提供了不少的帮助。非常感谢它们的作者。

- [teukka.tech - 从 init.vim 转到 init.lua](https://teukka.tech/luanvim.html)
- [2n.pl - 如何使用 Lua 编写 neovim 插件](https://www.2n.pl/blog/how-to-write-neovim-plugins-in-lua.md)
- [2n.pl - 如何使用 Lua 制作 neovim UI](https://www.2n.pl/blog/how-to-make-ui-for-neovim-plugins-in-lua)
- [ms-jpq - NeoVim 异步教程](https://ms-jpq.github.io/neovim-async-tutorial/)

### 相关插件

- [Vimpeccable](https://github.com/svermeulen/vimpeccable) - Plugin to help write your .vimrc in Lua
- [plenary.nvim](https://github.com/nvim-lua/plenary.nvim) - All the lua functions I don't want to write twice
- [popup.nvim](https://github.com/nvim-lua/popup.nvim) - An implementation of the Popup API from vim in Neovim
- [nvim_utils](https://github.com/norcalli/nvim_utils)
- [nvim-luadev](https://github.com/bfredl/nvim-luadev) - REPL/debug console for nvim lua plugins
- [nvim-luapad](https://github.com/rafcamlet/nvim-luapad) - Interactive real time neovim scratchpad for embedded lua engine
- [nlua.nvim](https://github.com/tjdevries/nlua.nvim) - Lua Development for Neovim
- [galaxyline.nvim](https://github.com/glepnir/galaxyline.nvim) - neovim statusline plugin written in lua
- [BetterLua.vim](https://github.com/euclidianAce/BetterLua.vim) - Better Lua syntax highlighting in Vim/NeoVim

## Lua 文件位置

Lua 文件通常位于您的 `runtimepath` 中的 `lua/` 文件夹中（对于大多数用户来说，在 *nix 系统上为 `~/.config/nvim/lua`，在 Windows 系统上为 `~/appdata/Local/nvim/lua`)。`Package.path` 和 `Package.cpath` 全局变量会自动调整为包含该文件夹下的 Lua 文件。这意味着您可以 `require()` 这些文件作为 Lua 模块

我们以下面的文件夹结构为例：

```text
📂 ~/.config/nvim
├── 📁 after
├── 📁 ftplugin
├── 📂 lua
│  ├── 🌑 myluamodule.lua
│  └── 📂 other_modules
│     ├── 🌑 anothermodule.lua
│     └── 🌑 init.lua
├── 📁 pack
├── 📁 plugin
├── 📁 syntax
└── 🇻 init.vim
```

下面的 Lua 代码将加载 `myluamodule.lua`

```lua
require('myluamodule')
```

注意没有 `.lua` 扩展名。

类似地，加载 `other_modules/anothermodule.lua` 的过程如下：

```lua
require('other_modules.anothermodule')
-- or
require('other_modules/anothermodule')
```

路径分隔符可以用点 `.` 表示，也可以用斜杠 `/` 表示。

文件夹如果包含 `init.lua` 文件，可以直接引用该文件夹而不必指定该文件的名称

```lua
require('other_modules') -- loads other_modules/init.lua
```

更多信息 `:help lua-require`

#### 警告

与 .vim 文件不同，.lua 文件不会自动从您的 `runtimepath` 目录中获取。相反，您必须从 Vimscript source/require 它们。计划增加 `init.lua` 文件加载选项，替代 `init.vim`：
- [Issue #7895](https://github.com/neovim/neovim/issues/7895)
- [Corresponding pull request](https://github.com/neovim/neovim/pull/12235)

#### 提示

多个 Lua 插件在它们的 `lua/` 文件夹中可能有相同的文件名。这可能会导致命名空间冲突。如果两个不同的插件有一个 `lua/main.lua` 文件，那么执行 `require('main')` 是不明确的：我们想要加载哪个文件？最好将您的配置或插件命名为顶级文件夹，
例如这样的形式：`lua/plugin_name/main.lua`。

#### 包说明

如果您是 `package` 特性的用户或基于它的插件管理器例如 [packer.nvim](https://github.com/wbthomason/packer.nvim)，[minpac](https://github.com/k-takata/minpac) 或 [vim-packager](https://github.com/kristijanhusak/vim-packager/)，那么在使用 Lua 插件时需要注意一些事情。`start` 文件夹中的包只有在源化您的 `init.vim` 之后才会加载。这意味着只有在 Neovim 处理完文件之后，才会将包添加到 `runtimepath` 中。如果插件期望
 `require` 一个 Lua 模块或调用自动加载的函数，这可能会导致问题。假设包 `start/foo` 有一个 `lua/bar.lua` 文件，从您的 `init.vim` 执行此操作将引发错误，因为 `runtimepath` 尚未更新。

```vim
lua require('bar')
```

你需要使用 `packadd! foo` 命令在 `require` 这个模块之前

```vim
packadd! foo
lua require('bar')
```

在 `Packadd` 后附加 `!` 表示 Neovim 会将包放在 `runtimepath` 中，而不会在其 `plugin` 或 `ftDetect` 目录下寻找任何脚本。

See also:
- `:help :packadd`
- [Issue #11409](https://github.com/neovim/neovim/issues/11409)

## 在 Vimscript 中使用 Lua

### :lua

该命令执行一段 Lua 代码

```vim
:lua require('myluamodule')
```

可以使用以下语法编写多行脚本：

```vim
echo "Here's a bigger chunk of Lua code"

lua << EOF
local mod = require('mymodule')
local tbl = {1, 2, 3}

for k, v in ipairs(tbl) do
    mod.method(v)
end

print(tbl)
EOF
```

See also:

- `:help :lua`
- `:help :lua-heredoc`

#### 警告

在 Vim 文件中编写 Lua 时，您不会得到正确的语法突出显示。使用 `:lua` 命令作为需要外部 Lua 文件的入口点可能会更方便。

### :luado

该命令执行一段 Lua 代码，该代码作用于当前缓冲区中的选中的行。如果未指定范围，则改为使用整个缓冲区。从块 `return` 的任何字符串都用于确定应该用什么替换每行。

以下命令会将当前缓冲区中的每一行替换为文本 `hello world`

```vim
:luado return 'hello world'
```

提供了两个隐式的 `line` 和 `linenr` 变量。`line` 是被迭代的行的文本，而 `linenr` 是它的编号。以下命令将可以被 2 整数的行转成大写：

```vim
:luado if linenr % 2 == 0 then return line:upper() end
```

See also:

- `:help :luado`

### :luafile

这个命令加载一个 lua 文件

```vim
:luafile ~/foo/bar/baz/myluafile.lua
```

类似于 Vim 的 `:source` 命令或 Lua 内置的 `dofile()` 函数。

See also:

- `:help :luafile`

#### luafile 对比 require():

您可能想知道 `lua request()` 和 `luafile` 之间的区别是什么，以及您是否应该使用其中一个而不是另一个。它们有不同的使用情形：

- `require()`:
    - 是内置的 Lua 函数，它允许你使用 Lua 的模块系统。
    - 使用 `Package.path` 变量搜索模块（如前所述，您可以使用 `runtimepath` 中的 `lua/` 文件夹内的 `required()` lua 脚本）
    - 跟踪已加载的模块，并防止第二次解析和执行脚本。如果您更改包含某个模块代码的文件，并在 Neovim 运行时再次尝试 `required()`，则该模块实际上不会更新。
- `:luafile`:
    - 是一个执行命令，它不支持模块。
    - 采用相对于当前窗口的工作目录的绝对或相对路径
    - 执行脚本的内容，而不管该脚本以前是否执行过

如果您想运行您正在处理的 Lua 文件，`:luafile` 很有用：

```vim
:luafile %
```

### luaeval()

`luaeval()` 是内置的 Vimscript 函数计算 Lua 表达式字符串并返回它的值。Lua 的数据类型自动转换为 Vimscript 类型（反之亦然）。

```vim
" 你可以将结果存储到一个变量中
let variable = luaeval('1 + 1')
echo variable
" 2
let concat = luaeval('"Lua".." is ".."awesome"')
echo concat
" 'Lua is awesome'

" Lua 中的 table 数组转成成 Vimscript 的 list
let list = luaeval('{1, 2, 3, 4}')
echo list[0]
" 1
echo list[1]
" 2
" 注意 Vimscript 的数组索引下标与 Lua 不同是从 0 开始的，Lua 中是从 1 开始

" Lua 中类似 dict 的 table 会被转成 Vimscript 中的 dict
let dict = luaeval('{foo = "bar", baz = "qux"}')
echo dict.foo
" 'bar'

" 布尔类型和 nil 是类似的
echo luaeval('true')
" v:true
echo luaeval('nil')
" v:null

" 您可以为 Lua 函数创建 Vimscript 别名
let LuaMathPow = luaeval('math.pow')
echo LuaMathPow(2, 2)
" 4
let LuaModuleFunction = luaeval('require("mymodule").myfunction')
call LuaModuleFunction()

" 还可以将 Lua 函数作为值传递给 Vim 函数
lua X = function(k, v) return string.format("%s:%s", k, v) end
echo map([1, 2, 3], luaeval("X"))
```

`luaeval()` 接受可选的第二个参数，该参数允许您将数据传递给表达式。然后您可以使用全局的 `_A` 从 Lua 访问该数据：

```vim
echo luaeval('_A[1] + _A[2]', [1, 1])
" 2

echo luaeval('string.format("Lua is %s", _A)', 'awesome')
" 'Lua is awesome'
```

See also:
- `:help luaeval()`

### v:lua

这个全局 Vim 变量允许您直接从 Vimscript 调用全局 Lua 函数。同样 Vim 数据类型被转换为 Lua 类型，反之亦然。

```vim
call v:lua.print('Hello from Lua!')
" 'Hello from Lua!'

let scream = v:lua.string.rep('A', 10)
echo scream
" 'AAAAAAAAAA'

" Requiring modules works
call v:lua.require('mymodule').myfunction()

" How about a nice statusline?
lua << EOF
function _G.statusline()
    local filepath = '%f'
    local align_section = '%='
    local percentage_through_file = '%p%%'
    return string.format(
        '%s%s%s',
        filepath,
        align_section,
        percentage_through_file
    )
end
EOF

set statusline=%!v:lua.statusline()

" Also works in expression mappings
lua << EOF
function _G.check_back_space()
    local col = vim.fn.col('.') - 1
    if col == 0 or vim.fn.getline('.'):sub(col, col):match('%s') then
        return true
    else
        return false
    end
end
EOF

inoremap <silent> <expr> <Tab>
    \ pumvisible() ? '\<C-n>' :
    \ v:lua.check_back_space() ? '\<Tab>' :
    \ completion#trigger_completion()
```

See also:
- `:help v:lua`
- `:help v:lua-call`

#### Caveats

此变量只能用于调用函数。以下代码将始终引发错误：

```vim
" Aliasing functions doesn't work
let LuaPrint = v:lua.print

" Accessing dictionaries doesn't work
echo v:lua.some_global_dict['key']

" Using a function as a value doesn't work
echo map([1, 2, 3], v:lua.global_callback)
```

## Vim 命名空间

Neovim 会暴露一个全局的 `vim` 变量来作为 Lua 调用 Vim 的 APIs 的入口。它还提供给用户一些额外的函数和子模块“标准库”

一些比较实用的函数和子模块如下：

- `vim.inspect`: 把 Lua 对象以更易读的方式打印（在打印 Lua table 时会很有用）
- `vim.regex`: 在 Lua 中使用 Vim 寄存器
- `vim.api`: 暴露 vim 的 API(`:h API`) 的模块（别的远程调用也是调用同样的 API)
- `vim.loop`: Neovim 的 event lopp 模块（使用 LibUV)
- `vim.lsp`: 控制内置 LSP 客户端的模块
- `vim.treesitter`: 暴露 tree-sitter 库中一些实用函数的模块

上面列举功能的并不全面。如果你想知道更多可行的操作可以看：`:help lua-stdlib` 和 `help lua-vim`。你也可以通过 `:lua print(vim.inspect(vim))` 获得所有可用模块

#### Tips

每次你想检查一个对象时到要用 `print(vim.inspect(x))` 是相当繁琐的。你可以你的配置中写一个全局的包装器函数来替代这个繁琐的过程

```lua
function _G.dump(...)
    local objects = vim.tbl_map(vim.inspect, {...})
    print(unpack(objects))
end
```

之后你就可以使用如下命令来快速检查对象内容了

```lua
dump({1, 2, 3})
```

```vim
:lua dump(vim.loop)
```

另外要注意的是，你可能会发现 Lua 会比其他语言少一些实用的内置函数（例如：`os.clock()`，返回以秒为单位，而不是以毫秒为单位的值）。仔细阅读 Neovim 提供的标准库和 `vim.fn` （后续还会有更多内容），里面可以会有你想要的东西。

## 在 Lua 中使用 Vimscript

### vim.api.nvim_eval()

此函数计算 Vimscript 表达式字符串并返回其值。Vimscript 数据类型自动转换为 Lua 类型（反之亦然）。

它等同于 vimscript 中的 `luaeval()` 函数

```lua
-- Data types are converted correctly
print(vim.api.nvim_eval('1 + 1')) -- 2
print(vim.inspect(vim.api.nvim_eval('[1, 2, 3]'))) -- { 1, 2, 3 }
print(vim.inspect(vim.api.nvim_eval('{"foo": "bar", "baz": "qux"}'))) -- { baz = "qux", foo = "bar" }
print(vim.api.nvim_eval('v:true')) -- true
print(vim.api.nvim_eval('v:null')) -- nil
```

**TODO**: is it possible for `vim.api.nvim_eval()` to return a `funcref`?

#### Caveats

与 `luaeval()` 不同，`vim.api.nvim_eval()` 不提供隐式 `_A` 变量来传递数据给表达式。

### vim.api.nvim_exec()

此函数用于计算 Vimscript 代码块。它接受一个包含要执行的源代码的字符串和一个布尔值，以确定代码的输出是否应该由函数返回（例如，您可以将输出存储在变量中）。

```lua
local result = vim.api.nvim_exec(
[[
let mytext = 'hello world'

function! MyFunction(text)
    echo a:text
endfunction

call MyFunction(mytext)
]],
true)

print(result) -- 'hello world'
```

**TODO**: The docs say that script-scope (`s:`) is supported, but running this snippet with a script-scoped variable throws an error. Why?

### vim.api.nvim_command()

此函数执行一个 EX 命令。它接受包含要执行的命令的字符串。

```lua
vim.api.nvim_command('new')
vim.api.nvim_command('wincmd H')
vim.api.nvim_command('set nonumber')
vim.api.nvim_command('%s/foo/bar/g')
```

注意：`vim.cmd` 是此函数的一个较短的别名

```lua
vim.cmd('buffers')
```

#### Tips

由于您必须将字符串传递给这些函数，因此通常需要添加转义的反斜杠：

```lua
vim.cmd('%s/\\Vfoo/bar/g')
```

非转义字符串更方便使用，它们不需要额外的转义反斜杠：

```lua
vim.cmd([[%s/\Vfoo/bar/g]])
```

## 管理 vim 的设置选项

### 使用 api 函数

Neovim 提供了一组 API 函数来设置选项或获取其当前值：

- 全局选项：
    - `vim.api.nvim_set_option()`
    - `vim.api.nvim_get_option()`
- 缓冲区选项：
    - `vim.api.nvim_buf_set_option()`
    - `vim.api.nvim_buf_get_option()`
- 窗口选项：
    - `vim.api.nvim_win_set_option()`
    - `vim.api.nvim_win_get_option()`

它们接受一个字符串，其中包含要设置或者要获取的选项的名称以及要将其设置为的值。

布尔选项（如 `(no)number`) 必须设置为 `true` 或 `false`：

```lua
vim.api.nvim_set_option('smarttab', false)
print(vim.api.nvim_get_option('smarttab')) -- false
```

字符串选项必须设置为字符串：

```lua
vim.api.nvim_set_option('selection', 'exclusive')
print(vim.api.nvim_get_option('selection')) -- 'exclusive'
```

数字选项必须接受数字类型

```lua
vim.api.nvim_set_option('updatetime', 3000)
print(vim.api.nvim_get_option('updatetime')) -- 3000
```

Buffer-local 和 Window-local 选项还需要缓冲区编号或窗口编号（使用 `0` 将设置 / 获取当前缓冲区 / 窗口的选项）：

```lua
vim.api.nvim_win_set_option(0, 'number', true)
vim.api.nvim_buf_set_option(10, 'shiftwidth', 4)
print(vim.api.nvim_win_get_option(0, 'number')) -- true
print(vim.api.nvim_buf_get_option(10, 'shiftwidth')) -- 4
```

### 使用元访问器

如果您想以更“惯用”的方式设置选项，可以使用一些元访问器。它们本质上包装了上述 API 函数，并允许您像处理变量一样操作选项：

- `vim.o.{option}`: 全局选项
- `vim.bo.{option}`: buffer-local 选项
- `vim.wo.{option}`: window-local 选项

```lua
vim.o.smarttab = false
print(vim.o.smarttab) -- false

vim.bo.shiftwidth = 4
print(vim.bo.shiftwidth) -- 4
```

您可以为缓冲区本地和窗口本地选项指定一个数字。如果未给出编号，则使用当前缓冲区 / 窗口：

```lua
vim.bo[4].expandtab = true -- same as vim.api.nvim_buf_set_option(4, 'expandtab', true)
vim.wo.number = true -- same as vim.api.nvim_win_set_option(0, 'number', true)
```

See also:
- `:help lua-vim-internal-options`

#### Caveats

**WARNING**：以下部分基于我做的几个实验。文档似乎没有提到这种行为，我也没有检查源代码来验证我的声明。
**TODO**：有谁能确认一下吗？

如果您只使用 `:set` 命令处理过选项，那么某些选项的行为可能会让您大吃一惊。
本质上，选项可以是全局的、缓冲区 / 窗口的局部的，或者同时具有全局和局部值。

`:setglobal` 命令用于设置选项的全局值。
`:setlocal` 命令用于设置选项的本地值。
`:set` 命令用于设置选项的全局和局部值。

这是 `:help :setglobal` 的简易表格：

| Command                 | global value | local value |
|-------------------------|--------------|-------------|
| :set option=value       | set          | set         |
| :setlocal option=value  | -            | set         |
| :setglobal option=value | set          | -           |

Lua 中没有 `:set` 命令的等价命令，可以全局设置，也可以本地设置。
您可能认为 `number` 选项是全局的，但文档将其描述为 `Windows-local`。这样的选项实际上是“粘性的”：当您打开一个新窗口时，它们的值是从当前窗口复制过来的。
因此，如果您要从您的 `init.lua` 设置选项，您应该这样做：

```lua
vim.wo.number = true
```

`shiftwidth`、`expandtab`、`undofile` 等本地到缓冲区的选项更容易混淆。假设您的 `init.lua` 包含以下代码：

```lua
vim.bo.expandtab = true
```

当你启动 Neovim 并开始编辑时，一切都很好：按下 `<Tab>` 键会插入空格，而不是制表符。打开另一个缓冲区，您会突然返回到选项卡...

在全局设置它具有相反的问题：

```lua
vim.o.expandtab = true
```

这次，您在第一次启动 Neovim 时插入选项卡。打开另一个缓冲区，然后按 `<Tab>` 即可实现预期效果。
简而言之，如果您想要正确的行为，“本地到缓冲区”的选项必须这样设置：

```lua
vim.bo.expandtab = true
vim.o.expandtab = true
```

See also:
- `:help :setglobal`
- `:help global-local`

**TODO**: Why does this happen? Do all buffer-local options behave this way? Might be related to [neovim/neovim#7658](https://github.com/neovim/neovim/issues/7658) and [vim/vim#2390](https://github.com/vim/vim/issues/2390). Also for window-local options: [neovim/neovim#11525](https://github.com/neovim/neovim/issues/11525) and [vim/vim#4945](https://github.com/vim/vim/issues/4945)

## 管理 vim 的内部变量

### 使用 api 函数

与选项非常的相似，内部变量也有自己的 api 函数：

- 全局变量 (`g:`):
    - `vim.api.nvim_set_var()`
    - `vim.api.nvim_get_var()`
    - `vim.api.nvim_del_var()`
- 缓冲区变量 (`b:`):
    - `vim.api.nvim_buf_set_var()`
    - `vim.api.nvim_buf_get_var()`
    - `vim.api.nvim_buf_del_var()`
- 窗口变量 (`w:`):
    - `vim.api.nvim_win_set_var()`
    - `vim.api.nvim_win_get_var()`
    - `vim.api.nvim_win_del_var()`
- 选项卡变量 (`t:`):
    - `vim.api.nvim_tabpage_set_var()`
    - `vim.api.nvim_tabpage_get_var()`
    - `vim.api.nvim_tabpage_del_var()`
- 预定义的 vim 变量 (`v:`):
    - `vim.api.nvim_set_vvar()`
    - `vim.api.nvim_get_vvar()`

除了预定义的 Vim 变量外，还可以删除它们（等同于 Vimscript 中的 `:unlet`)。局部变量 (`l:`)、脚本变量 (`s:`) 和函数参数 (`a:`) 不能操作，因为它们只在 Vim 脚本上下文中有意义，Lua 有自己的作用域规则。
如果您不熟悉这些变量的作用，请参考 `:help internal-variables` 对其进行详细介绍。
这些函数接受一个字符串，该字符串包含要设置 / 获取 / 删除的变量的名称以及要将其设置为的值。

```lua
vim.api.nvim_set_var('some_global_variable', { key1 = 'value', key2 = 300 })
print(vim.inspect(vim.api.nvim_get_var('some_global_variable'))) -- { key1 = "value", key2 = 300 }
vim.api.nvim_del_var('some_global_variable')
```

范围为缓冲区、窗口或选项卡的变量会接受一个数字类型的参数 (0 意味着设置 / 获取 / 删除当前缓冲区 / 窗口 / 选项卡页的变量）：

```lua
vim.api.nvim_win_set_var(0, 'some_window_variable', 2500)
vim.api.nvim_tab_set_var(3, 'some_tabpage_variable', 'hello world')
print(vim.api.nvim_win_get_var(0, 'some_window_variable')) -- 2500
print(vim.api.nvim_buf_get_var(3, 'some_tabpage_variable')) -- 'hello world'
vim.api.nvim_win_del_var(0, 'some_window_variable')
vim.api.nvim_buf_del_var(3, 'some_tabpage_variable')
```

### 使用元访问器

使用这些元访问器可以更直观地操作内部变量：

- `vim.g.{name}`: 全局变量
- `vim.b.{name}`: 缓冲区变量
- `vim.w.{name}`: 窗口变量
- `vim.t.{name}`: 选项卡变量
- `vim.v.{name}`: 预定义变量

```lua
vim.g.some_global_variable = {
    key1 = 'value',
    key2 = 300
}

print(vim.inspect(vim.g.some_global_variable)) -- { key1 = "value", key2 = 300 }
```

删除变量只需要将它的值设置为 nil

```lua
vim.g.some_global_variable = nil
```

#### Caveats

您不能从存储在这些变量之一的字典中添加 / 更新 / 删除键。 例如，这段 Vimscript 代码不能按预期工作：

```vim
let g:variable = {}
lua vim.g.variable.key = 'a'
echo g:variable
" {}
```

这是个已知的问题

- [Issue #12544](https://github.com/neovim/neovim/issues/12544)

## 调用 Vimscript 函数

### vim.call()

`vim.call()` 调用 Vimscript 函数。这可以是内置 Vim 函数，也可以是用户函数。同样，数据类型在 Lua 和 Vimscript 之间来回转换。它接受函数名，后跟要传递给该函数的参数：

```lua
print(vim.call('printf', 'Hello from %s', 'Lua'))

local reversed_list = vim.call('reverse', { 'a', 'b', 'c' })
print(vim.inspect(reversed_list)) -- { "c", "b", "a" }

local function print_stdout(chan_id, data, name)
    print(data[1])
end

vim.call('jobstart', 'ls', { on_stdout = print_stdout })

vim.call('my#autoload#function')
```

See also:
- `:help vim.call()`

### vim.fn.{function}()

`vim.fn` 的功能与 `vim.call()` 完全相同，但看起来更像是原生 Lua 函数调用

```lua
print(vim.fn.printf('Hello from %s', 'Lua'))

local reversed_list = vim.fn.reverse({ 'a', 'b', 'c' })
print(vim.inspect(reversed_list)) -- { "c", "b", "a" }

local function print_stdout(chan_id, data, name)
    print(data[1])
end

vim.fn.jobstart('ls', { on_stdout = print_stdout })
```

Hashes `#` 不是 Lua 中识别符的有效字符，因此必须使用以下语法调用 autoload 函数：

```lua
vim.fn['my#autoload#function']()
```

See also:
- `:help vim.fn`

#### Tips

Neovim 有一个强大的内置函数库，这些函数对插件非常有用。按字母顺序排列的函数列表参见 `:help vim-function`，按主题分组的函数列表参见 `:help function-list`。

#### Caveats

一些应该返回布尔值的 Vim 函数返回 `1` 或 `0`。这在 Vimscript 中不是问题，因为 `1` 是真的，而 `0` 是假的，支持如下结构：

```vim
if has('nvim')
    " do something...
endif
```

然而，在 Lua 中，只有 `false` 和 `nil` 被认为是假的，数字的计算结果总是 `true`，无论它们的值是什么。您必须显式检查 `1` 或 `0`：

```lua
if vim.fn.has('nvim') == 1 then
    -- do something...
end
```

## 定义映射

Neovim 提供了一系列的 api 函数来设置获取和删除映射：

- 全局映射：
    - `vim.api.nvim_set_keymap()`
    - `vim.api.nvim_get_keymap()`
    - `vim.api.nvim_del_keymap()`
- 缓冲区映射：
    - `vim.api.nvim_buf_set_keymap()`
    - `vim.api.nvim_buf_get_keymap()`
    - `vim.api.nvim_buf_del_keymap()`

让我们从 `vim.api.nvim_set_keymap()` 和 `vim.api.nvim_buf_set_keymap()` 开始，传递给函数的第一个参数是一个包含映射生效模式名称的字符串：

| String value           | Help page     | Affected modes                           | Vimscript equivalent |
|------------------------|---------------|------------------------------------------|----------------------|
| `''` (an empty string) | `mapmode-nvo` | Normal, Visual, Select, Operator-pending | `:map`               |
| `'n'`                  | `mapmode-n`   | Normal                                   | `:nmap`              |
| `'v'`                  | `mapmode-v`   | Visual and Select                        | `:vmap`              |
| `'s'`                  | `mapmode-s`   | Select                                   | `:smap`              |
| `'x'`                  | `mapmode-x`   | Visual                                   | `:xmap`              |
| `'o'`                  | `mapmode-o`   | Operator-pending                         | `:omap`              |
| `'!'`                  | `mapmode-ic`  | Insert and Command-line                  | `:map!`              |
| `'i'`                  | `mapmode-i`   | Insert                                   | `:imap`              |
| `'l'`                  | `mapmode-l`   | Insert, Command-line, Lang-Arg           | `:lmap`              |
| `'c'`                  | `mapmode-c`   | Command-line                             | `:cmap`              |
| `'t'`                  | `mapmode-t`   | Terminal                                 | `:tmap`              |

第二个参数是包含映射左侧的字符串（触发映射中定义的命令的键或键集）。空字符串相当于 `<Nop>`，表示禁用键位。

第三个参数是包含映射右侧（要执行的命令）的字符串。

最后一个参数是一个表，包含 `:help :map-arguments` 中定义的映射的布尔值选项（包括 `noremap`，不包括 `buffer`)。

缓冲区-本地映射也将缓冲区编号作为其第一个参数(`0` 设置当前缓冲区的映射）。

```lua
vim.api.nvim_set_keymap('n', '<leader><Space>', ':set hlsearch!<CR>', { noremap = true, silent = true })
-- :nnoremap <silent> <leader><Space> :set hlsearch<CR>

vim.api.nvim_buf_set_keymap(0, '', 'cc', 'line(".") == 1 ? "cc" : "ggcc"', { noremap = true, expr = true })
-- :noremap <buffer> <expr> cc line('.') == 1 ? 'cc' : 'ggcc'
```

`vim.api.nvim_get_keymap()` 接受一个字符串，该字符串包含您想要映射列表的模式的短名称（见上表）。返回值是包含该模式的所有全局映射的表。

```lua
print(vim.inspect(vim.api.nvim_get_keymap('n')))
-- :verbose nmap
```

`vim.api.nvim_buf_get_keymap()` 将缓冲区编号作为其第一个参数 (`0` 将获取当前缓冲区的映射）

```lua
print(vim.inspect(vim.api.nvim_buf_get_keymap(0, 'i')))
-- :verbose imap <buffer>
```

`vim.api.nvim_del_keymap()` 获取映射左侧的模式。

```lua
vim.api.nvim_del_keymap('n', '<leader><Space>')
-- :nunmap <leader><Space>
```

同样，`vim.api.nvim_buf_del_keymap()` 以缓冲区编号作为第一个参数，其中 `0` 表示当前缓冲区。

```lua
vim.api.nvim_buf_del_keymap(0, 'i', '<Tab>')
-- :iunmap <buffer> <Tab>
```

## 定义用户命令

目前在 Lua 中没有创建用户命令的接口。不过已经在计划内：

- [Pull request #11613](https://github.com/neovim/neovim/pull/11613)

目前，您最好使用 Vimscript 创建命令。

## 定义自动命令

AUGROUP 和 AUTOCOMMAND 还没有接口，但正在处理

- [Pull request #12378](https://github.com/neovim/neovim/pull/12378)

不过你可以使用 `vim.api.nvim_command` 来创建自动命令，例如：

```lua
-- Create autocmd
vim.api.nvim_command('autocmd FileType * do something')

-- Create augroup
vim.api.nvim_command('augroup groupname')
vim.api.nvim_command('autocmd do something')
vim.api.nvim_command('augroup end')
```

## 定义语法高亮

`vim.api.nvim_buf_add_highlight` 为 buffer 指定的行和位置添加高亮

Neovim 0.5 集成 treesitter，对于语法高亮相比之前使用正则的方式更加的高效，[nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter)

- `:help lua-treesitter`

我个人制作的主题 [zephyr-nvim](https://github.com/glepnir/zephyr-nvim) 语法高亮基于 nvim-treesitter

## General tips and recommendations

**TODO**:
- Hot-reloading of modules
- `vim.validate()`?
- Add stuff about unit tests? I know Neovim uses the [busted](https://olivinelabs.com/busted/) framework, but I don't know how to use it for plugins
- Best practices? I'm not a Lua wizard so I wouldn't know
- How to use LuaRocks packages ([wbthomason/packer.nvim](https://github.com/wbthomason/packer.nvim)?)

## Miscellaneous

### vim.loop

`vim.loop` 是暴露 LibUV 接口的模块。

```lua
local stop_signal = false

local function set_variable()
  for i = 1, 10, 1 do
    print(i)
    if i == 5 then
      stop_signal= true
      break
    end
  end
end

local function start_close_timer()
  local timer = vim.loop.new_timer()
  timer:start(10,1,vim.schedule_wrap(function()
  if stop_signal == true and timer:is_closing() == false then
    print('stop timer and close it')
    timer:stop()
    timer:close()
  end
  end))
end

set_variable()
start_close_timer()
```

- [Official documentation for LibUV](https://docs.libuv.org/en/v1.x/)
- [Luv documentation](https://github.com/luvit/luv/blob/master/docs.md)
- [teukka.tech - Using LibUV in Neovim](https://teukka.tech/vimloop.html)

See also:
- `:help vim.loop`

### vim.lsp

`vim.lsp` 是内置的 lsp 库。官方的 lsp 配置插件 [neovim/nvim-lspconfig](https://github.com/neovim/nvim-lspconfig/)

- [nvim-lua/completion-nvim](https://github.com/nvim-lua/completion-nvim)
- [nvim-lua/diagnostic-nvim](https://github.com/nvim-lua/diagnostic-nvim)

See also:
- `:help lsp`

### vim.treesitter

`vim.treesitter` 是 [Tree-sitter](https://tree-sitter.github.io/tree-sitter/) 的 neovim 集成，如果你想了解更多可以参考 [presentation (38:37)](https://www.youtube.com/watch?v=Jes3bD6P0To).

The [nvim-treesitter](https://github.com/nvim-treesitter/) organisation hosts various plugins taking advantage of the library.

See also:
- `:help lua-treesitter`

### Transpilers

One advantage of using Lua is that you don't actually have to write Lua code! There is a multitude of transpilers available for the language.

- [Moonscript](https://moonscript.org/)

Probably one of the most well-known transpilers for Lua. Adds a lots of convenient features like classes, list comprehensions or function literals. The [svermeulen/nvim-moonmaker](https://github.com/svermeulen/nvim-moonmaker) plugin allows you to write Neovim plugins and configuration directly in Moonscript.

- [Fennel](https://fennel-lang.org/)

A lisp that compiles to Lua. You can write configuration and plugins for Neovim in Fennel with the [Olical/aniseed](https://github.com/Olical/aniseed) plugin. Additionally, the [Olical/conjure](https://github.com/Olical/conjure) plugin provides an interactive development environment that supports Fennel (among other languages).

Other interesting projects:

- [TypeScriptToLua/TypeScriptToLua](https://github.com/TypeScriptToLua/TypeScriptToLua)
- [teal-language/tl](https://github.com/teal-language/tl)
- [Haxe](https://haxe.org/)
- [SwadicalRag/wasm2lua](https://github.com/SwadicalRag/wasm2lua)
- [hengestone/lua-languages](https://github.com/hengestone/lua-languages)
