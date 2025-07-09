# Vim脚本实战教程

---

> 技术人解决问题的方式，从来不是换编辑器，而是**把编辑器改造成自己想要的样子**。

本教程不是给新手看的“如何退出Vim”，而是给**已经卡在`.vimrc`里挣扎的中级玩家**的生存手册。我们直奔主题，用实战代码解决真实痛点。

## 1. 暴力破解派：最直接的效率提升

这一派的特点是立竿见影，用最短的配置解决最常见的问题。

#### 示例一：一键运行当前脚本

每次修改完脚本都要手动到终端里执行 `python a.py`？太慢了。

```vim
" 将<Leader>p映射为执行当前Python文件
nnoremap <Leader>p :!python %<CR>
```
- `nnoremap`: 创建一个非递归的普通模式映射。这是推荐的映射方式，以避免意外的递归行为。
- `<Leader>p`: 你的前导键（通常是 `\`）加上 `p`。这是一个常用的自定义快捷键前缀。
- `:!python %`: 执行外部命令 `python`。`%` 是一个特殊的Vim寄存器，它会被自动替换为当前文件的名。
- `<CR>`: 代表回车键（Carriage Return），用于立即执行前面的命令。

现在，在任何Python文件里按下 `\p` 试试。对于其他语言，如 `go run %` 或 `node %`，同理可得。

#### 示例二：保存时自动格式化代码

代码洁癖的福音，保证团队代码风格一致性的利器。

```vim
" 在保存Go文件之前，自动执行 :GoFmt
autocmd BufWritePre *.go :GoFmt
```
- `autocmd`: 自动命令（Auto Command），Vim的事件驱动核心。它能在特定事件发生时自动执行命令。
- `BufWritePre`: 事件名称，指“缓冲区写入文件之前”（Buffer Write Pre）。
- `*.go`: 模式（Pattern），表示该自动命令只对所有以 `.go` 结尾的文件生效。
- `:GoFmt`: 要执行的命令。你需要先安装提供了 `:GoFmt` 命令的插件（如 `vim-go`）。对于其他语言或工具，可以是 `:!prettier %` 或 `:!black %` 等。

## 2. 插件解剖课：自己动手，丰衣足食

插件很好，但知道它怎么工作更好。有时候，一个小功能不需要引入一个庞大的插件。

#### 示例一：为什么我的`<C-h>`映射会失效？

这是一个经典问题。你可能在 `.vimrc` 里写了 `nnoremap <C-h> <C-w>h`，想用 `Ctrl-h` 在窗口间向左切换，但它就是不工作，或者行为诡异。

**原因：终端序列冲突。**
在很多终端（Terminal）里，`Ctrl-h` 发送的信号和 `Backspace`（退格键）是完全一样的。Vim收到的就是退格键的信号，而不是你期望的 `Ctrl-h`。

**诊断方法：**
在Vim的插入模式下，按下 `Ctrl-v` 然后再按 `Ctrl-h`，看看屏幕上出现的是什么。如果出现的是 `^?` 或者 `^H`，就说明存在冲突。`Ctrl-v` 的作用是“原样输入”，让下一个字符不被解释。

**解决方案（之一）：**
在你的终端配置（如 `.zshrc` 或 `.bashrc`）中明确区分它们，或者干脆在Vim里换一个不冲突的映射，比如 `<Leader>h`。

#### 示例二：用30行代码实现一个极简目录树？

这可能有些夸张，但理解其核心原理并不难。一个基础的目录树至少需要：
1.  一个函数，用于读取指定目录的内容 (`readdir()`)。
2.  打开一个新窗口（`split` 或 `vsplit`）。
3.  将目录内容写入这个新窗口的缓冲区 (`setline()`)。
4.  创建映射，让你可以用回车键打开文件或进入子目录。

这是一个概念性的实现，展示了基本思路：
```vim
function! SimpleFileBrowser(path)
    " 在垂直分屏中打开新窗口
    vsplit
    " 设置缓冲区为临时、不可修改
    setlocal buftype=nofile bufhidden=wipe noswapfile nomodifiable
    " 读取目录内容并设置到缓冲区
    let files = readdir(a:path)
    call setline(1, files)
    " ... 此处省略了按键映射、错误处理和目录/文件区分逻辑 ...
endfunction
```
这只是一个骨架，但它揭示了许多文件浏览器插件的本质：**读取文件系统，然后操作Vim的缓冲区来模拟UI**。

## 3. 黑暗魔法区：深入缓冲区编程

警告：以下操作可能导致Vim崩溃或数据丢失，请在实验前保存好你的工作。

#### 示例一：用`getline()`和`setline()`直接操作缓冲区

想象一下，你要写一个函数，把当前行所有的小写字母 `a` 替换为大写 `A`。

```vim
function! CapitalizeA()
    " 获取当前行号
    let lnum = line('.')
    " 获取当前行的内容
    let line_content = getline(lnum)
    " 替换内容
    let new_content = substitute(line_content, 'a', 'A', 'g')
    " 将新内容写回当前行
    call setline(lnum, new_content)
endfunction

" 映射一个快捷键来调用它
nnoremap <Leader>A :call CapitalizeA()<CR>
```
- `getline(lnum)`: 获取指定行号 `lnum` 的文本。
- `setline(lnum, content)`: 将指定行 `lnum` 的内容设置为 `content`。
- `line('.')`: 获取当前光标所在行的行号。 `.` 在Vim里通常代表“当前位置”。

这比 `:s/a/A/g` 繁琐，但它展示了如何用编程方式精确控制文本，这是编写复杂插件的基础。

#### 示例二：动态生成快捷键

假设你希望用数字键 `1`, `2`, `3`, `4` 快速切换到第1、2、3、4个窗口。

```vim
" 使用循环动态创建映射
for i in range(1, 4)
    exec 'nnoremap ' . i . ' :' . i . 'wincmd w'
endfor
```
- `for i in range(1, 4)`: 一个从1到4的循环。
- `exec`: 执行一个由字符串拼接而成的命令。
- `'nnoremap ' . i . ' :' . i . 'wincmd w'`: 在每次循环中，这个表达式会生成 `"nnoremap 1 :1wincmd w"`, `"nnoremap 2 :2wincmd w"` 等字符串，然后由 `exec` 执行。

---

欢迎 Star 我的 [GitHub项目](https://github.com/yahao333/vim_tutorial)，更欢迎提PR补充你的邪道技巧。
