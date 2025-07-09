# Vim Script Practical Tutorial

---

> The way a tech professional solves problems is never by changing the editor, but by **transforming the editor into what they want it to be**.

This tutorial is not for beginners wondering "how to exit Vim." Instead, it's a survival guide for **intermediate users already struggling in their `.vimrc`**. We'll get straight to the point, using practical code to solve real pain points.

## 1. The Brute Force School: Immediate Efficiency Boosts

This school is characterized by quick, tangible results, solving common problems with minimal configuration.

#### Example One: Run Current Script with a Single Keypress

Do you manually go to the terminal and run `python a.py` every time you modify a script? That's too slow.

```vim
" Map <Leader>p to execute the current Python file
nnoremap <Leader>p :!python %<CR>
```
- `nnoremap`: Creates a non-recursive normal mode mapping. This is the recommended mapping method to avoid unexpected recursive behavior.
- `<Leader>p`: Your leader key (usually `\`) followed by `p`. This is a common prefix for custom shortcuts.
- `:!python %`: Executes the external command `python`. `%` is a special Vim register that automatically expands to the current file's name.
- `<CR>`: Represents the Carriage Return key, used to immediately execute the preceding command.

Now, try pressing `\p` in any Python file. The same principle applies to other languages, such as `go run %` or `node %`.

#### Example Two: Auto-Format Code on Save

A blessing for code purists and a powerful tool for ensuring consistent team code style.

```vim
" Before saving a Go file, automatically execute :GoFmt
autocmd BufWritePre *.go :GoFmt
```
- `autocmd`: Auto Command, Vim's event-driven core. It allows you to automatically execute commands when specific events occur.
- `BufWritePre`: The event name, meaning "before the buffer is written to a file" (Buffer Write Pre).
- `*.go`: The pattern, indicating that this auto command only applies to files ending with `.go`.
- `:GoFmt`: The command to execute. You'll need to have a plugin that provides the `:GoFmt` command installed (e.g., `vim-go`). For other languages or tools, this could be `:!prettier %` or `:!black %`, etc.

## 2. Plugin Dissection Class: Do It Yourself, Be Self-Sufficient

Plugins are great, but understanding how they work is even better. Sometimes, a small feature doesn't require pulling in a massive plugin.

#### Example One: Why Does My `<C-h>` Mapping Fail?

This is a classic problem. You might have `nnoremap <C-h> <C-w>h` in your `.vimrc`, intending to use `Ctrl-h` to switch to the left window, but it just doesn't work, or behaves strangely.

**Reason: Terminal Sequence Conflict.**
In many terminals, the signal sent by `Ctrl-h` is exactly the same as `Backspace`. Vim receives the backspace signal, not the `Ctrl-h` you expect.

**Diagnosis Method:**
In Vim's insert mode, press `Ctrl-v` and then `Ctrl-h`. See what appears on the screen. If it's `^?` or `^H`, there's a conflict. `Ctrl-v` acts as a "literal input" key, preventing the next character from being interpreted.

**Solution (One of Many):**
Explicitly differentiate them in your terminal configuration (e.g., `.zshrc` or `.bashrc`), or simply use a non-conflicting mapping in Vim, such as `<Leader>h`.

#### Example Two: Implement a Minimal File Browser in 30 Lines of Code?

This might be an exaggeration, but understanding its core principles isn't difficult. A basic file browser would at least need:
1.  A function to read the contents of a specified directory (`readdir()`).
2.  Opening a new window (`split` or `vsplit`).
3.  Writing the directory contents into this new window's buffer (`setline()`).
4.  Creating mappings to allow you to open files or enter subdirectories with the Enter key.

Here's a conceptual implementation, demonstrating the basic idea:
```vim
function! SimpleFileBrowser(path)
    " Open a new window in a vertical split
    vsplit
    " Set the buffer as temporary and unmodifiable
    setlocal buftype=nofile bufhidden=wipe noswapfile nomodifiable
    " Read directory contents and set them to the buffer
    let files = readdir(a:path)
    call setline(1, files)
    " ... Key mappings, error handling, and directory/file differentiation logic omitted here ...
endfunction
```
This is just a skeleton, but it reveals the essence of many file browser plugins: **reading the file system and then manipulating Vim's buffers to simulate a UI**.

## 3. The Dark Magic Zone: Deep Dive into Buffer Programming

Warning: The following operations may cause Vim to crash or lead to data loss. Please save your work before experimenting.

#### Example One: Directly Manipulating Buffers with `getline()` and `setline()`

Imagine you want to write a function that replaces all lowercase `a`'s with uppercase `A`'s on the current line.

```vim
function! CapitalizeA()
    " Get the current line number
    let lnum = line('.')
    " Get the content of the current line
    let line_content = getline(lnum)
    " Replace content
    let new_content = substitute(line_content, 'a', 'A', 'g')
    " Write the new content back to the current line
    call setline(lnum, new_content)
endfunction

" Map a shortcut to call it
nnoremap <Leader>A :call CapitalizeA()<CR>
```
- `getline(lnum)`: Retrieves the text of the specified line number `lnum`.
- `setline(lnum, content)`: Sets the content of the specified line `lnum` to `content`.
- `line('.')`: Gets the line number of the current cursor position. `.` in Vim usually refers to the "current position."

While this is more cumbersome than `:s/a/A/g`, it demonstrates how to programmatically control text with precision, which is fundamental for writing complex plugins.

#### Example Two: Dynamically Generating Shortcuts

Suppose you want to use the number keys `1`, `2`, `3`, `4` to quickly switch to the 1st, 2nd, 3rd, and 4th windows, respectively.

```vim
" Dynamically create mappings using a loop
for i in range(1, 4)
    exec 'nnoremap ' . i . ' :' . i . 'wincmd w'
endfor
```
- `for i in range(1, 4)`: A loop from 1 to 4.
- `exec`: Executes a command constructed from a string.
- `'nnoremap ' . i . ' :' . i . 'wincmd w'`: In each iteration, this expression generates strings like `"nnoremap 1 :1wincmd w"`, `"nnoremap 2 :2wincmd w"`, etc., which are then executed by `exec`.

---

Feel free to Star my [GitHub project](https://github.com/yahao333/vim_tutorial), and even more welcome to submit PRs with your own dark arts and tricks.
