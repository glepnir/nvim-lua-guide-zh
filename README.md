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
    - [加载 Lua 文件](#加载 Lua 文件)
      - [加载 Lua 文件对比 require():](#加载 Lua 文件对比 require():)
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
    - [vim.cmd](#vim.cmd)
    - [vim.api.nvim_replace_termcodes()](#vim.api.nvim_replace_termcodes())
  - [管理 vim 的设置选项](#管理-vim-的设置选项)
    - [使用 api 函数](#使用-api-函数)
    - [使用元访问器](#使用元访问器)
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

本指南假定您使用的是 Neovim 0.5+

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
- [ms-jpq - NeoVim 异步教程](https://ms-jpq.github.io/lua-async-await/index.html)

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

### init.lua

Neovim 支持从 `init.lua` 文件加载配置而不是通常的 `init.vim` 文件。  

注意：`init.lua` 文件是完全可选的。Neovim 仍然支持从 `init.vim` 加载配置。请记住，Neovim 的一些功能还没有 100% 暴露给 Lua 模块部分。 

### 模块

Lua 模块通常位于您的 `runtimepath` 中的 `lua/` 文件夹中（对于大多数用户来说，在 *nix 系统上为 `~/.config/nvim/lua`，在 Windows 系统上为 `~/appdata/Local/nvim/lua`)。这意味着您可以 `require()` 这些文件作为 Lua 模块

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

加载一个不存在的模块或者加载的模块有语法错误会直接中止当前正在执行的脚本。`pcall()` 函数可以用来处理这类错误

```lua
local ok, _ = pcall(require, 'module_with_error')
if not ok then
  -- not loaded
end
```

更多信息请参见：

* [`:help lua-require`](https://neovim.io/doc/user/lua.html#lua-require)

#### 提示

多个 Lua 插件在它们的 `lua/` 文件夹中可能有相同的文件名。这可能会导致命名空间冲突。如果两个不同的插件有一个 `lua/main.lua` 文件，那么执行 `require('main')` 是不明确的：我们想要加载哪个文件？最好将您的配置或插件命名为顶级文件夹，
例如这样的形式：`lua/plugin_name/main.lua`。

### 运行时文件

和 Vimscript 文件很像，位于 ` runtimepath` 中的一些特殊目录中的 Lua 文件可以被 Neovim 自动加载。目前有以下这些特殊目录：

* `colors/`
* `compiler/`
* `ftplugin/`
* `indent/`
* `plugin/`
* `syntax/`

注意：在同一个运行时目录中，`*.vim` 文件会先于所有的 `*.lua` 文件被加载。

更多信息请参见：

* [`:help 'runtimepath'`](https://neovim.io/doc/user/options.html#'runtimepath')
* [`:help load-plugins`](https://neovim.io/doc/user/starting.html#load-plugins)

#### 提示

因为运行时文件并不基于 Lua 模块系统，所以两个不同的插件都拥有 `plugins/main.lua` 文件是没有任何问题的。

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

注意：每个 `:lua` 命令都有它自己独立的作用域，在一条 `:lua` 命令中使用 `local` 关键字声明的变量是无法在这条命令之外访问的。如下代码所示

```vim
:lua local foo = 1
:lua print(foo)
" prints 'nil' instead of '1'
```

注意：Lua 中的 `print()` 函数的行为类似于 `:echomsg` 命令。它的输出会被保存在消息历史中，可以使用 `:slient` 命令来抛弃输出。

更多信息请参见：

- [`:help :lua`](https://neovim.io/doc/user/lua.html#Lua)
- [`:help :lua-heredoc`](https://neovim.io/doc/user/lua.html#:lua-heredoc)

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

更多信息请参见：

- [`:help :luado`](https://neovim.io/doc/user/lua.html#:luado)

### 加载 Lua 文件

Neovim 提供了三种执行命令来加载 Lua 文件

* `:luafile`
* `:source`
* `:runtime`

其中 `:luafile` 和 `:source` 非常类似：

```vim
:luafile ~/foo/bar/baz/myluafile.lua " 加载 myluafile.lua
:luafile %                           " 加载当前正在处理的文件
:source ~/foo/bar/baz/myluafile.lua
:source %
```

两种命令都可以指定文件范围，可以只执行脚本的一部分

```vim
:1,10source
```

`:runtime` 与上述两种命令有所不同，它使用 `'runtimepath'` 选项来决定加载哪个文件。更多细节信息请参见  [`:help :runtime`](https://neovim.io/doc/user/repeat.html#:runtime)

更多信息请参见:

- [`:help :luafile`](https://neovim.io/doc/user/lua.html#:luafile)
- [`:help :source`](https://neovim.io/doc/user/repeat.html#:source)
- [`:help :runtime`](https://neovim.io/doc/user/repeat.html#:runtime)

####  加载 Lua 文件对比 require():

您可能想知道调用 `require()` 函数和使用上述命令加载之间的区别是什么，以及您是否应该使用其中一个而不是另一个。它们有不同的使用情形：

- `require()`:
    - 是内置的 Lua 函数，它允许你使用 Lua 的模块系统
    - 在 `'runtimepath'` 中的 `lua/` 文件夹中搜索模块
    - 跟踪已加载的模块，并防止第二次解析和执行脚本。如果您更改包含某个模块代码的文件，并在 Neovim 运行时再次尝试 `required()`，则该模块实际上不会更新
- `:luafile`/`:source`/`:runtime`:
    - 是一个执行命令，它不支持模块
    - 执行脚本的内容，而不管该脚本以前是否执行过
    - `:luafile`/`:source` 命令采用相对于当前窗口的工作目录的绝对或相对路径
    - `:runtime` 命令使用 `'runtimepath'` 选项来寻找文件

同时，通过 `:source`/`:runtime` 命令（不包括  `:luafile` ）加载或者从运行时目录被自动加载的文件会显示在 `:scriptnames` 和 `--startuptime` 中。

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

更多信息请参见：
- [`:help luaeval()`](https://neovim.io/doc/user/lua.html#luaeval())

### v:lua

这个全局 Vim 变量允许您直接从 Vimscript 调用全局 Lua 函数（[`_G`](https://www.lua.org/manual/5.1/manual.html#pdf-_G)）。同样 Vim 数据类型被转换为 Lua 类型，反之亦然。

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

" 通过使用单引号包围模块名并省略 require 的括号来调用 Lua 模块中的函数
call v:lua.require'module'.foo()
```

更多信息请参见：
- [`:help v:lua`](https://neovim.io/doc/user/eval.html#v:lua)
- [`:help v:lua-call`](https://neovim.io/doc/user/lua.html#v:lua-call)

#### Caveats

`v:lua` 变量只能用于调用函数。以下代码将始终引发错误：

```vim
" 不适用于别名一个函数
let LuaPrint = v:lua.print

" 不适用于访问 dict
echo v:lua.some_global_dict['key']

" 不适用于将函数作为值使用
echo map([1, 2, 3], v:lua.global_callback)
```

#### 提示

在配置文件中，可以通过设置 `let g:vimsyn_embed = 'l'` 实现 .vim 文件中的 Lua 语法高亮。关于此选项的更多信息请参见 [`:help g:vimsyn_embed`](https://neovim.io/doc/user/syntax.html#g:vimsyn_embed)

## Vim 命名空间

Neovim 会暴露一个全局的 `vim` 变量来作为 Lua 调用 Vim 的 APIs 的入口。它还提供给用户一些额外的函数和子模块“标准库”

一些比较实用的函数和子模块如下：

- `vim.inspect`: 把 Lua 对象以更易读的方式打印（在打印 Lua table 时会很有用）
- `vim.regex`: 在 Lua 中使用 Vim 正则表达式
- `vim.api`: 暴露 vim 的 API(`:h API`) 的模块（别的远程调用也是调用同样的 API)
- `vim.ui`: 可被插件覆写的 UI 相关函数
- `vim.loop`: Neovim 的 event lopp 模块（使用 LibUV)
- `vim.lsp`: 控制内置 LSP 客户端的模块
- `vim.treesitter`: 暴露 tree-sitter 库中一些实用函数的模块

上面列举功能的并不全面。如果你想知道更多可行的操作可以参见：[`:help lua-stdlib`](https://neovim.io/doc/user/lua.html#lua-stdlib) 和 [`:help lua-vim`](https://neovim.io/doc/user/lua.html#lua-vim)。你也可以通过 `:lua print(vim.inspect(vim))` 获得所有可用模块。API 函数的详细文档请参见 [`:help api-global`](https://neovim.io/doc/user/api.html#api-global)

#### Tips

每次你想检查一个对象时到要用 `print(vim.inspect(x))` 是相当繁琐的。你可以你的配置中写一个全局的包装器函数来替代这个繁琐的过程（在 Neovim 0.7.0+ 中，你可以使用内建的 `vim.pretty_print()` ，请参见 [`:help vim.pretty_print()`](https://neovim.io/doc/user/lua.html#vim.pretty_print())）

```lua
function _G.put(...)
    local objects = {}
    for i = 1, select('#', ...) do
        local v = select(i, ...)
        table.insert(objects, vim.inspect(v))
     end

     print(table.concat(objects, '\n'))
     return ...
end
```

之后你就可以使用如下命令来快速检查对象内容了

```lua
put({1, 2, 3})
```

```vim
:lua put(vim.loop)
```

此外，你也可以使用 `:lua` 命令通过在 Lua 表达式前加上 `=` 来美观地打印它

```vim
:lua =vim.loop
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

#### Caveats

在 Neovim 0.6.0 之前，`nvim_exec` 不支持 script-local 的变量 （`s:`） 。

### vim.api.nvim_command()

此函数执行一个 EX 命令。它接受包含要执行的命令的字符串。

```lua
vim.api.nvim_command('new')
vim.api.nvim_command('wincmd H')
vim.api.nvim_command('set nonumber')
vim.api.nvim_command('%s/foo/bar/g')
```

### vim.cmd()

`vim.api.nvim_exec()`  的别名。只需要命令部分的参数，`output` 参数始终为 `false`

```vim
vim.cmd('buffers')
vim.cmd([[
let g:multiline_list = [
            \ 1,
            \ 2,
            \ 3,
            \ ]

echo g:multiline_list
]])
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

### vim.api.nvim_replace_termcodes()

这个 API 函数允许你转义终端代码和 Vim 键码。

你可能见过这样的映射：

```vim
inoremap <expr> <Tab> pumvisible() ? "\<C-N>" : "\<Tab>"
```

在 Lua 中实现相同的功能是比较困难的。你可能会像这样实现

```lua
function _G.smart_tab()
    return vim.fn.pumvisible() == 1 and [[\<C-N>]] or [[\<Tab>]]
end

vim.api.nvim_set_keymap('i', '<Tab>', 'v:lua.smart_tab()', {expr = true, noremap = true})
```

然后你会发现这样实现上述映射只会单纯插入 `\<Tab>` 和 `\<C-N>` 两个字符串字面量……

能够转义键码实际上是 Vimscript 的一项功能。  除了许多编程语言常用的转义序列（如  `\r`、`\42` 或 `\x10`）外，Vimscript 中的 `expr-quotes`（用双引号括起来的字符串）允许你转义 Vim 键码的易读形式。

Lua 中并没有内置这种功能。幸运的是，Neovim 提供了一个用来转义终端代码和 Vim 键码的 API 函数：`nvim_replace_termcodes()`

```lua
print(vim.api.nvim_replace_termcodes('<Tab>', true, true, true))
```

这样调用过于冗长，我们可以创建一个方便重复调用的包装：

```lua
-- The function is called `t` for `termcodes`.
-- You don't have to call it that, but I find the terseness convenient
local function t(str)
    -- Adjust boolean arguments as needed
    return vim.api.nvim_replace_termcodes(str, true, true, true)
end

print(t'<Tab>')
```

回到先前的例子，我们可以这样实现那个映射

```lua
local function t(str)
    return vim.api.nvim_replace_termcodes(str, true, true, true)
end

function _G.smart_tab()
    return vim.fn.pumvisible() == 1 and t'<C-N>' or t'<Tab>'
end

vim.api.nvim_set_keymap('i', '<Tab>', 'v:lua.smart_tab()', {expr = true, noremap = true})
```

如果我们使用 `vim.keymap.set()` 函数（Neovim 0.7.0+）来设置的话就不需要转义键码，`vim.keymap.set()` 默认会自动转义返回值中的键码（`expr` 选项设置为 `true`）：

```lua
vim.keymap.set('i', '<Tab>', function()
    return vim.fn.pumvisible() == 1 and '<C-N>' or '<Tab>'
end, {expr = true})
```

更多信息请参见：

* [`:help keycodes`](https://neovim.io/doc/user/intro.html#keycodes)
* [`:help expr-quote`](https://neovim.io/doc/user/eval.html#expr-quote)
* [`:help nvim_replace_termcodes()`](https://neovim.io/doc/user/api.html#nvim_replace_termcodes())

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

- [`vim.o`](https://neovim.io/doc/user/lua.html#vim.o): 行为类似于 `:let &{option-name}`
- [`vim.go`](https://neovim.io/doc/user/lua.html#vim.go): 行为类似于 `:let &g:{option-name}`
- [`vim.bo`](https://neovim.io/doc/user/lua.html#vim.bo): 适用于 buffer-local 选项，行为类似于 `:let &l:{option-name}`
- [`vim.wo`](https://neovim.io/doc/user/lua.html#vim.wo): 适用于 window-local 选项，行为类似于 `:let &l:{option-name}`

```lua
vim.o.smarttab = false -- let &smarttab = v:false
print(vim.o.smarttab) -- false
vim.o.isfname = vim.o.isfname .. ',@-@' -- on Linux: let &isfname = &isfname .. ',@-@'
print(vim.o.isfname) -- '@,48-57,/,.,-,_,+,,,#,$,%,~,=,@-@'

vim.bo.shiftwidth = 4
print(vim.bo.shiftwidth) -- 4
```

您可以为缓冲区本地和窗口本地选项指定一个数字。如果未给出编号，则使用当前缓冲区 / 窗口：

```lua
vim.bo[4].expandtab = true -- same as vim.api.nvim_buf_set_option(4, 'expandtab', true)
vim.wo.number = true -- same as vim.api.nvim_win_set_option(0, 'number', true)
```

这些访问器还有更为复杂的 `vim.opt*` 变体，它们为在 Lua 中设置选项提供了更为灵活便利的机制，就像你在 `init.vim` 中使用的 `:set`/`:setglobal`/`:setlocal`:

* `vim.opt`: 行为类似于 `:set`
* `vim.opt_global`: 行为类似于 `:setglobal`
* `vim.opt_local`: 行为类似于 `:setlocal`

```lua
vim.opt.smarttab = false
print(vim.opt.smarttab:get()) -- false
```

一些选项也可以通过 Lua 的 table 来设置：

```lua
vim.opt.completeopt = {'menuone', 'noselect'}
print(vim.inspect(vim.opt.completeopt:get())) -- { "menuone", "noselect" }
```

对于类似于 list/map/set 的选项，它们对应的包装器还实现了各种方法与元方法，行为类似于 Vimscript 中的 `:set+=`/`:set^=`/`:set-=`。

```lua
vim.opt.shortmess:append({ I = true })
-- alternative form:
vim.opt.shortmess = vim.opt.shortmess + { I = true }

vim.opt.whichwrap:remove({ 'b', 's' })
-- alternative form:
vim.opt.whichwrap = vim.opt.whichwrap - { 'b', 's' }
```

关于 `vim.opt` 的更多信息请参见：[`:help vim.opt`](https://neovim.io/doc/user/lua.html#vim.opt)

更多信息请参见：

- [`:help lua-vim-options`](https://neovim.io/doc/user/lua.html#lua-vim-options)

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
