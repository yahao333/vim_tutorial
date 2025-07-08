# Getting Started with Vim Script Customization

Welcome to the world of Vim script customization! This tutorial will guide you from scratch on how to build a powerful and personalized Vim environment by writing your own scripts.

## Table of Contents

- [Step One: Create Your Vim Configuration File](#step-one-create-your-vim-configuration-file)
- [Step Two: Basic Customization - Key Mappings](#step-two-basic-customization---key-mappings)
- [Step Three: Write Your First Vim Script Function](#step-three-write-your-first-vim-script-function)
- [Step Four: Script Organization and Management](#step-four-script-organization-and-management)
- [Step Five: Using Autocommands](#step-five-using-autocommands)
- [Important Notes](#important-notes)
- [Advanced Vim Customization Tutorial](ADVANCED_VIM_TUTORIAL_EN.md)
- [Minimal Vimrc Configuration Tutorial](MINIMAL_VIMRC_TUTORIAL_EN.md)

## Step One: Create Your Vim Configuration File

Vim's power lies in its high customizability. Almost all customizations are done by modifying Vim's configuration file.

### What is a Vim Configuration File?

A Vim configuration file is a plain text file that Vim automatically loads upon startup. You can add various commands and settings to this file to change Vim's behavior and appearance.

On macOS and Linux systems, this file is usually `~/.vimrc`. For Neovim users, the configuration file is located at `~/.config/nvim/init.vim`.

### Create and Edit Your First Configuration

Let's get started! You can create and open your Vim configuration file by executing the following command in your terminal:

```bash
vim ~/.vimrc
```

Now, add the following simple configuration code to the file:

```vim
set number
```

This line of code makes Vim display line numbers. Save and close the file (type `:wq` in Vim).

Now, reopen Vim, and you'll find that line numbers have appeared on the left side of the editor. Congratulations, you've successfully taken the first step in customizing Vim!

## Step Two: Basic Customization - Key Mappings

Is typing `:w` to save a file tedious every time? In Vim, we can map common commands to hotkeys to improve efficiency.

### Key Mapping Modes

Vim has multiple operating modes, the most commonly used are Normal Mode, Insert Mode, and Visual Mode. We can set different hotkeys for different modes.

- `nmap`: Effective only in Normal Mode.
- `imap`: Effective only in Insert Mode.
- `vmap`: Effective only in Visual Mode.
- `map`: Effective in both Normal Mode and Visual Mode.

Generally, we recommend using `nmap` to avoid unexpected behavior in other modes.

### Set Your First Hotkey

Let's map `<leader>w` to the command for saving files. The `leader` key is a special prefix key, which defaults to `\`, but many users prefer to set it to a more convenient key, such as a comma `,`.

Add the following code to your `.vimrc` file to set the `leader` key:

```vim
let mapleader = ','
```

Then, add the key mapping:

```vim
nmap <leader>w :w<CR>
```

Here, `<CR>` represents the Enter key. Now, save your `.vimrc` file and reopen Vim. In Normal Mode, press `,w`, and you'll find that the file has been saved!

## Step Three: Write Your First Vim Script Function

Vim scripts are not just simple configurations and mappings; you can also write your own functions to perform more complex tasks. Vim9 script introduces modern function definition syntax, making script writing more intuitive.

### Function Definition Syntax

Vim9 script uses `def` and `enddef` to define functions. For example:

```vim
def SayHello()
    echo "Hello, Vim!"
enddef
```

### Write and Call Your Function

Let's add this `SayHello()` function to your `.vimrc` file:

```vim
def SayHello()
    echo "Hello, Vim!"
enddef

nmap <leader>h :call SayHello()<CR>
```

Save your `.vimrc` file and reload Vim. Now, when you press `,h`, you'll see "Hello, Vim!" output in Vim's command-line area.

This simple function demonstrates the power of Vim scripts: you can encapsulate a series of commands into a function and call it easily with a hotkey.

## Step Four: Script Organization and Management

As your Vim configuration becomes more complex, keeping all scripts in `.vimrc` can become difficult to manage. Vim provides a standard directory structure to help you organize your scripts.

### `~/.vim/` Directory Structure

Vim automatically loads files from specific subdirectories under `~/.vim/` upon startup. The two most commonly used subdirectories are:

- `plugin/`: Stores plugin scripts. Vim automatically loads all `.vim` files under the `plugin/` directory when it starts. These scripts usually contain global settings, mappings, or functions that are loaded at Vim startup and remain available.
- `autoload/`: Stores functions that are loaded on demand. When you call a function in a Vim script, if Vim cannot find the function in memory, it will try to find the corresponding file in the `autoload/` directory. This mechanism avoids loading all functions at Vim startup, thereby improving startup speed.

### Practice: Organize Your Scripts

Let's move the `SayHello()` function from `.vimrc` to a separate `autoload` file and create a simple `plugin` script.

First, create the necessary directories:

```bash
mkdir -p ~/.vim/plugin
mkdir -p ~/.vim/autoload
```

Then, create a `~/.vim/plugin/my_settings.vim` file and add some global settings:

```vim
" ~/.vim/plugin/my_settings.vim

" Map save hotkey
nmap <leader>w :w<CR>
```

Next, create a `~/.vim/autoload/my_functions.vim` file and move the `SayHello()` function into it. Note that functions in the `autoload` directory need to be named in the format `filename#functionname`:

```vim
" ~/.vim/autoload/my_functions.vim

def my_functions#SayHello()
    echo "Hello from autoload!"
enddef
```

Finally, modify your `.vimrc` file to ensure `let mapleader = ','` exists, remove the `SayHello()` function definition, and update the hotkey mapping to call the function from `autoload`:

```vim
" ~/.vimrc

set number
let mapleader = ',' " Ensure leader key is set here

" Remove SayHello() function definition

nmap <leader>h :call my_functions#SayHello()<CR>
```

Save all files and reload Vim. Now, when you press `,h`, you will still see "Hello from autoload!" output. This indicates that Vim successfully loaded your function from the `autoload` directory.

This way, you can better organize your Vim scripts, making them easier to maintain and extend.

## Step Five: Using Autocommands

Autocommands are a very powerful feature in Vim that allows you to automatically execute a series of commands when specific events occur. For example, when opening a file, saving a file, or entering a specific mode.

### `autocmd` Concepts and Usage

The basic syntax of `autocmd` is:

```vim
autocmd [event] [file_pattern] [command]
```

- `event`: The event that triggers the autocommand, such as `BufReadPost` (after reading a buffer), `BufWritePre` (before writing a buffer), etc.
- `file_pattern`: A pattern to match filenames, such as `*.py` (all Python files), `*.md` (all Markdown files), etc.
- `command`: The Vim command to execute when the event and file pattern match.

### Practice: Set Automatic Indentation for Python Files

Let's add an autocommand to automatically set indentation to 4 spaces when opening `.py` files:

Add the following code to your `.vimrc` file:

```vim
" ~/.vimrc

filetype plugin indent on

" ... (previous configurations)

autocmd FileType python setlocal tabstop=4 shiftwidth=4 expandtab
```

- `FileType python`: This is a special event that triggers when Vim recognizes the file type as `python`.
- `setlocal tabstop=4 shiftwidth=4 expandtab`: This is the command to execute. `setlocal` means it only applies to the current buffer, `tabstop` sets the width of the Tab key, `shiftwidth` sets the width of automatic indentation, and `expandtab` converts Tab keys to spaces.

Save your `.vimrc` file and reload Vim. Now, when you open a `.py` file, Vim will automatically apply these indentation settings.

Autocommands greatly extend Vim's automation capabilities. You can trigger custom behaviors on various events according to your needs.

## Important Notes

### How to Reload Vim Configuration?

After each modification to `.vimrc` or other Vim script files, you need to make Vim reload these configurations for them to take effect. There are several ways to do this:

1.  **Restart Vim**: The simplest and most direct way is to close the current Vim session and then reopen Vim. This forces Vim to reread all configuration files.

2.  **Use the `:source` command**: Inside Vim, you can use the `:source` command to reload specific configuration files. For example, to reload your `.vimrc` file, you can type:

    ```vim
    :source ~/.vimrc
    ```

    If you modify scripts under the `plugin` or `autoload` directories, restarting Vim is usually a more reliable option to ensure all scripts are loaded correctly.

### Debugging Vim Scripts

When writing Vim scripts, you may encounter errors. Here are some common debugging techniques:

-   **`echo` command**: Use the `echo` command to output variable values or debugging information in Vim's command-line area.
-   **`echomsg` command**: Similar to `echo`, but adds messages to Vim's message history, which you can view with `:messages`.
-   **`:debug` command**: This is a powerful debugging tool that allows you to step through scripts. For example, `:debug source ~/.vimrc` will let you execute your `.vimrc` file step by step.

Hope these notes help you better customize and manage your Vim environment!

---

## Further Learning

If you have mastered the basics in this tutorial and wish to explore Vim's powerful features further, please refer to our [Advanced Vim Customization Tutorial](ADVANCED_VIM_TUTORIAL.md).

```