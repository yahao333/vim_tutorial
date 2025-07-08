# Vim脚本定制入门教程

欢迎来到Vim脚本定制的世界！本教程将引导你从零开始，学习如何通过编写自己的脚本来打造一个强大且个性化的Vim环境。

## 第一步：创建你的Vim配置文件

Vim的强大之处在于其高度的可定制性。几乎所有的定制都是通过修改Vim的配置文件来完成的。

### 什么是Vim配置文件？

Vim配置文件是一个纯文本文件，Vim在启动时会自动加载它。你可以在这个文件中添加各种命令和设置，来改变Vim的行为和外观。

在macOS和Linux系统上，这个文件通常是 `~/.vimrc`。对于使用Neovim的用户，配置文件位于 `~/.config/nvim/init.vim`。

### 创建并编辑你的第一个配置

让我们开始吧！你可以在终端中执行以下命令来创建并打开你的Vim配置文件：

```bash
vim ~/.vimrc
```

现在，将以下这行简单的配置代码添加到文件中：

```vim
set number
```

这行代码的作用是让Vim显示行号。保存并关闭文件（在Vim中输入 `:wq`）。

现在，重新打开Vim，你会发现编辑器左侧已经出现了行号。恭喜你，你已经成功迈出了定制Vim的第一步！

## 第二步：基础定制 - 快捷键映射

每次都输入 `:w` 来保存文件是不是有点繁琐？在Vim中，我们可以将常用的命令映射到快捷键上，以提高效率。

### 快捷键映射的模式

Vim有多种工作模式，最常用的是普通模式（Normal Mode）、插入模式（Insert Mode）和可视模式（Visual Mode）。我们可以为不同的模式设置不同的快捷键。

- `nmap`: 只在普通模式下生效。
- `imap`: 只在插入模式下生效。
- `vmap`: 只在可视模式下生效。
- `map`:  在普通模式和可视模式下都生效。

通常，我们推荐使用 `nmap`，以避免在其他模式下产生意想不到的行为。

### 设置你的第一个快捷键

让我们将 `<leader>w` 映射为保存文件的命令。`leader` 键是一个特殊的前缀键，默认是 `\`，但很多用户喜欢将其设置为更方便的按键，比如逗号 `,`。

首先，在你的 `.vimrc` 文件中添加以下代码来设置 `leader` 键：

```vim
let mapleader = ','
```

然后，添加快捷键映射：

```vim
nmap <leader>w :w<CR>
```

这里的 `<CR>` 代表回车键。现在，保存你的 `.vimrc` 文件并重新打开Vim。在普通模式下，按下 `,w`，你就会发现文件被保存了！

## 第三步：编写你的第一个Vim脚本函数

Vim脚本不仅仅是简单的配置和映射，你还可以编写自己的函数来执行更复杂的任务。Vim9 script 引入了现代的函数定义语法，让脚本编写更加直观。

### 函数定义语法

Vim9 script 使用 `def` 和 `enddef` 来定义函数。例如：

```vim
def SayHello()
    echo "Hello, Vim!"
enddef
```

### 编写并调用你的函数

让我们在 `.vimrc` 文件中添加这个 `SayHello()` 函数：

```vim
def SayHello()
    echo "Hello, Vim!"
enddef

nmap <leader>h :call SayHello()<CR>
```

保存 `.vimrc` 文件并重新加载Vim。现在，在普通模式下按下 `,h`，你会在Vim的命令行区域看到 "Hello, Vim!" 的输出。

这个简单的函数展示了Vim脚本的强大之处：你可以将一系列命令封装成一个函数，并通过快捷键轻松调用。

## 第四步：脚本的组织与管理

随着你的Vim配置越来越复杂，将所有脚本都放在 `.vimrc` 中会变得难以管理。Vim提供了一个标准的目录结构来帮助你组织脚本。

### `~/.vim/` 目录结构

Vim会在启动时自动加载 `~/.vim/` 目录下的特定子目录中的文件。最常用的两个子目录是：

- `plugin/`: 存放插件脚本。Vim启动时会自动加载 `plugin/` 目录下的所有 `.vim` 文件。这些脚本通常包含一些全局的设置、映射或函数，它们在Vim启动时就会被加载并一直可用。
- `autoload/`: 存放按需加载的函数。当你在Vim脚本中调用一个函数时，如果Vim在内存中找不到这个函数，它会尝试在 `autoload/` 目录下查找对应的文件。这种机制可以避免在Vim启动时加载所有函数，从而提高启动速度。

### 实践：组织你的脚本

让我们将 `SayHello()` 函数从 `.vimrc` 中移到一个独立的 `autoload` 文件中，并创建一个简单的 `plugin` 脚本。

首先，创建必要的目录：

```bash
mkdir -p ~/.vim/plugin
mkdir -p ~/.vim/autoload
```

然后，创建一个 `~/.vim/plugin/my_settings.vim` 文件，并添加一些全局设置：

```vim
" ~/.vim/plugin/my_settings.vim

" 设置 leader 键
let mapleader = ','

" 映射保存快捷键
nmap <leader>w :w<CR>
```

接下来，创建一个 `~/.vim/autoload/my_functions.vim` 文件，并将 `SayHello()` 函数移入其中。注意，`autoload` 目录下的函数需要以 `文件名#函数名` 的形式命名：

```vim
" ~/.vim/autoload/my_functions.vim

def my_functions#SayHello()
    echo "Hello from autoload!"
enddef
```

最后，修改你的 `.vimrc` 文件，移除 `SayHello()` 函数的定义，并更新快捷键映射以调用 `autoload` 中的函数：

```vim
" ~/.vimrc

set number

" 移除之前的 leader 键设置和保存映射，它们现在在 plugin 目录中
" 移除 SayHello() 函数定义

nmap <leader>h :call my_functions#SayHello()<CR>
```

保存所有文件并重新加载Vim。现在，当你按下 `,h` 时，仍然会看到 "Hello from autoload!" 的输出。这表明Vim成功地从 `autoload` 目录加载了你的函数。

通过这种方式，你可以更好地组织你的Vim脚本，使其更易于维护和扩展。

```
