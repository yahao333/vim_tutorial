# Advanced Vim Play: From "Knowing How" to "Mastering"

## Foreword: Don't Use Vim Like Sublime

Many say Vim has a steep learning curve, but once mastered, efficiency greatly improves. This is only half true. If you merely memorize a bunch of hotkeys and use Vim like another "VS Code" or "Sublime," you're just a skilled laborer, far from true efficiency.

Vim's essence lies in its **design philosophy**: **using "language" and "composition" to replace repetitive operations**. It's not just a simple editor; it's an "operating system" for text processing. The goal of this tutorial isn't to make you memorize more commands, but to help you understand the ideas behind these advanced features, and truly start "thinking" about how to operate in Vim. This is the key to going from "knowing how" to "mastering."

If you're not ready to challenge your habits, content with "strolling" through code with your mouse and arrow keys, then you can close this page now. If you want to become a "Hacker" who thoroughly understands their tools and pursues ultimate efficiency, then let's begin.

---

## Chapter One: The "Dao" of Text Operations

### Section One: Text Objects - Vim's "Grammar"

Are you still moving character by character with `h/j/k/l` or jumping word by word with `w/b` to select code? That's inefficient! It's like speaking English by spitting out one letter at a time. Vim provides you with a "grammar"—**Text Objects**—allowing you to directly operate on the "semantic units" of your code.

- `iw` / `aw`: an inner word / a word
- `ip` / `ap`: an inner paragraph / a paragraph
- `i(` / `a(`: content inside parentheses / the entire parentheses
- `i{` / `a{`: content inside curly braces / the entire curly braces
- `i"` / `a"`: content inside quotes / the entire quotes

**Practical Thinking:**
You see a function call `process(calculate(x, y), z)` and want to modify the two parameters of `calculate`. What's your first reaction?
- **Novice Thinking**: Move cursor to `x`, delete, modify, then move to `y`...
- **Vim Thinking**: Move the cursor anywhere inside the inner parentheses, a `ci(` (Change Inner Parenthesis) command, and all content inside the parentheses is gone, directly entering insert mode for you to rewrite. Clean and swift.

**Remember, action verbs like `c` (change), `d` (delete), `y` (yank), `v` (visual select) can all be combined with text objects as "nouns." `di{` means delete all code inside curly braces, `ya"` means yank the entire quoted string. This is Vim's language, and you must learn it.**

### Section Two: Registers - Vim's "Multi-functional Clipboard"

Still using one clipboard for everything? When you want to swap two blocks of code, do you first copy A, paste A, then go back to copy B, paste B, only to find your original clipboard content overwritten?

Vim's registers completely solve this problem. It provides dozens of "named clipboards."

- `"a` - `"z`: 26 named registers, your private clipboards.
- `"+`: System clipboard, a bridge for interacting with external applications.
- `"_`: Black hole register, a place where things go in but never come out, perfect for delete operations.
- `"0`: Yank-specific register, content yanked by the `y` command also goes here.

**Practical Thinking:**
Refactoring code, you need to swap the implementation of function A with function B.
1.  Inside function A, `da{` (delete a block). The code is deleted, but don't panic, it's in the unnamed register.
2.  `"Ad{` (delete a block into register A). We store it in register `a`.
3.  Go inside function B, similarly `"Bd{` to store it in register `b`.
4.  Now, in A's empty shell, `"Bp` paste the content of register `b`.
5.  In B's empty shell, `"Ap` paste the content of register `a`.

The entire process is fluid, requiring no temporary files or back-and-forth jumps. This is how professionals work.

### Section Three: Regular Expressions - The "Nuclear Weapon" of Text Processing

If you're still using simple string search and replace, your Vim is utilizing less than 10% of its power. Regular expressions are Vim's true "nuclear weapon" for text processing. They allow you to precisely locate and manipulate text through pattern matching.

Vim's regular expression syntax is somewhat "unique," but once mastered, you'll find it incredibly powerful.

- `\v`: "very magic" mode. Recommended! It makes regex syntax closer to the PCRE (Perl Compatible Regular Expressions) you're familiar with, reducing the nightmare of backslashes.
- `\m`: "magic" mode. Vim's default mode, many special characters need to be escaped with a backslash.
- `\V`: "very nomagic" mode. Almost all characters are interpreted literally; special characters need to be escaped with a backslash.

**Practical Thinking:**
- **Scenario 1: Batch modifying function signatures**
  Suppose you have an old API with a function signature `old_func(arg1, arg2)`, and now you need to change it to `new_func(arg2, arg1)`.
  `:s/\vold_func\(([^,]+), ([^)]+)\)/new_func(\2, \1)/g`
  - `\v`: Enable very magic mode.
  - `([^,]+)`: Capture the first parameter (one or more non-comma characters).
  - `([^)]+)`: Capture the second parameter (one or more non-closing-parenthesis characters).
  - `\2, \1`: Swap the order of captured groups during replacement.

- **Scenario 2: Deleting all blank lines and comment lines**
  `:g/^\s*\($\|\/\/\|#\)/d`
  - `^\s*`: Zero or more spaces at the beginning of the line.
  - `\($\|\/\/\|#\)`: Matches end of line (blank line), `//` (C/C++/Java comments), or `#` (Python/Shell comments).
  - `d`: Delete the matching lines.

**Regular expressions are one of Vim's souls. Master them, and you'll truly understand Vim's power, allowing you to navigate and manipulate text freely, omnipotently.**

---

## Chapter Two: The "Art" of Workflow

### Section One: Macros - Your "Personal Automation Assistant"

When coding, you often encounter repetitive modifications with fixed patterns. For example, adding a `public static final` prefix to a bunch of constants. How do you do it? Copy and paste one by one?

Macros are for this. They can record a series of your operations and then repeat them indefinitely.

**Practical Thinking:**
You have CSV data and want to convert it into JSON key-value pairs.
```
id,name,age
1,Alice,20
2,Bob,25
```
Target format: `{"id": 1, "name": "Alice", "age": 20},`

1.  Position the cursor at the beginning of the `id` line.
2.  `qa` Start recording macro to register `a`.
3.  `^` Go to line start, `I{` insert `{`.
4.  `s/,/": /` Replace the first comma with `": `.
5.  `w` Jump to the next word, `i"` insert quote.
6.  Repeat a few times until the line is processed into `{"id": 1, "name": "Alice", "age": 20}`.
7.  `A,` Add comma at line end.
8.  `j` Move to next line.
9.  `q` Stop recording.

Now, just press `@@` (repeat last macro), and Vim will automatically process all remaining lines for you. Hundreds or thousands of lines, done in seconds. **This is automation!**

### Section Two: Global Command (`:g`) - The Ultimate Batch Processing Command

If you think macros are powerful, the `:global` command (`:g`) will show you Vim's true terror. Its purpose is: **to execute a specified command on all lines matching a certain pattern.**

Syntax: `:g/pattern/command`

**Practical Thinking:**
- **Scenario 1:** Delete all lines containing `DEBUG` logs in a file.
  `:g/DEBUG/d`
- **Scenario 2:** Add a separator `---` under all Markdown level-two headings (starting with `## `).
  `:g/^## /normal A\n---\n`
  Here, `normal A\n---\n` simulates, in normal mode, moving to the end of the line (`A`), inserting a newline (`\n`), typing the separator, and then another newline.
- **Scenario 3:** Copy all lines matching `TODO` to a new file named `todo.txt`.
  `:g/TODO/ .write >> todo.txt`

The power of the `global` command lies in its ability to combine with any of Vim's Ex commands (commands starting with a colon). This is the ultimate weapon for structured operations across an entire file.

### Section Three: Command-line Window (`q:`) - Say Goodbye to Repetitive Typing

Do you often mistype commands in Vim's command line, or want to re-execute a complex command from before? Then repeatedly press up/down arrows, or retype it?

`q:` command is your savior! It opens a separate window listing all your command history. You can edit, copy, paste, and execute any historical command just like editing normal text.

**Practical Thinking:**
1.  Press `q:`, a new window will pop up with your command history.
2.  Find that `:s/old/new/g` command you mistyped earlier.
3.  Like editing normal text, use `cw` to change `old` to `correct_old`.
4.  Move the cursor to the modified line, press Enter (`<CR>`), and Vim will execute the corrected command.

**This is not just convenience; it's zero tolerance for "repetition." Vim's philosophy is: any repetitive operation should be automated or simplified.**

### Section Four: Windows and Buffers - Managing Your "Workspace"

Stop using Vim like Notepad, opening only one file at a time. And don't be like some IDEs, opening dozens of tabs and clicking around with the mouse. Vim has more efficient ways to manage your workspace: **Window** and **Buffer**.

- **Buffer**: Every file you open has a buffer in memory. They are your "background," always available even if not displayed on screen.
- **Window**: A window is a "viewport" into a buffer. You can open multiple windows to display different buffers, or different parts of the same buffer.

**Practical Thinking:**
1.  Use `:e file.c` to open a file; it's now in a buffer.
2.  Use `:sp header.h` to split the window horizontally and open a header file. Now you have two windows, two buffers.
3.  Use `:ls` to view a list of all open buffers.
4.  Use `:b 3` or `:b file_name` to quickly switch to another buffer in the current window, without needing an "open file" command.
5.  `Ctrl-w` + `hjkl` to move seamlessly between windows.

**Core Idea: A file is opened once and stays in a buffer. After that, all operations are on buffers and windows, which is much faster than repeatedly opening/closing files or switching tabs. This is the correct way to manage projects.**

---

## Chapter Three: The "Soul" of Vim

### Section One: Dancing with the Shell - The Power of `!`

A true Hacker's editor and Shell are seamlessly integrated. Vim's `!` command is this bridge.

- `:!cmd`: Execute an external shell command. E.g., `:!ls -l`.
- `:%!cmd`: Filter the entire file with `cmd`. This is the most powerful usage.

**Practical Thinking:**
- **Scenario 1:** You have a messy JSON. Want to format it?
  `:%!python -m json.tool` or `:%!jq .`. The entire file content will be instantly replaced with the formatted output.
- **Scenario 2:** You want to sort and deduplicate the content of the current file.
  `:%!sort -u`
- **Scenario 3:** You want to pass the word under the cursor as an argument to an external command.
  `let word = expand("<cword>") | execute ":!grep -r " . word . " ."`

**Don't keep your editor isolated. Combine Vim with the powerful Unix/Linux toolchain, and you'll get a result where 1+1 >> 2.**

### Section Two: Autocommands (`autocmd`) - Making Vim "Alive"

Are you still manually setting indentation for different file types? Or forgetting to run a code formatter every time you save a Python file? That's primitive!

Autocommands (`autocmd`) make Vim automatically execute commands based on **events**. For example, "when opening a file," "before saving a file," or "when entering insert mode."

**Practical Thinking:**
```vim
" When reloading .vimrc, automatically clear old autocommands to prevent duplicate definitions
augroup MyAutoCmds
  autocmd!
augroup END

" Set different indentation for different file types
autocmd FileType python setlocal shiftwidth=4 tabstop=4
autocmd FileType javascript setlocal shiftwidth=2 tabstop=2

" When saving .go files, automatically run go fmt
autocmd BufWritePre *.go :!go fmt %
```
- `augroup`: This is a good habit. Group your autocommands, and `autocmd!` can clear all commands within the group with one command, preventing duplicate loading.
- `BufWritePre`: The event "before a buffer is written to a file."
- `%`: Represents the current filename.

**A well-configured `autocmd` can automate a large number of repetitive manual operations. This is the ultimate meaning of customization. Your editor should be a smart assistant that understands your habits, not a dumb tool that requires you to do everything manually.**

### Section Three: Advanced Vimscript - Crafting Your "Exclusive Weapon"

If you're content with using pre-written plugins, you'll always remain a "user." Vimscript is the key to transforming you from a "user" into a "creator." It's Vim's built-in programming language, allowing you to write your own functions, commands, and even more complex logic, turning Vim into an "exclusive weapon" perfectly tailored to your workflow.

- **Variable Scope**: `g:` (global), `b:` (buffer), `w:` (window), `t:` (tab page), `s:` (script), `l:` (local), `a:` (function argument), `v:` (Vim predefined). Understanding these is crucial for writing robust code.
- **Data Types**: Lists `[]` and Dictionaries `{}`. Vimscript also supports these modern programming language features.
- **Control Flow**: `if-else`, `for`, `while`. Basic programming logic.
- **Error Handling**: `try-catch`. Makes your scripts more robust.

**Practical Thinking:**
Suppose you frequently need to copy the current file path to the system clipboard, but Vim doesn't have a direct command. You can write one yourself:
```vim
" Define a function to copy the current file path to the system clipboard
def! CopyFilePath()
  let @+ = expand("%:p")
  echo "File path copied to clipboard: " . @+
enddef

" Define a command to easily call this function
command! CopyPath call CopyFilePath()
```
Now, you just need to type `:CopyPath` in Vim, and the absolute path of the current file will be copied to your system clipboard. **This is the beauty of Vimscript—making Vim work for you, not you being constrained by Vim.**

### Section Four: Plugins and Plugin Managers - Standing on the Shoulders of Giants

While we encourage self-reliance, there's no need to reinvent the wheel. Vim has a vast and active plugin ecosystem. Plugin managers are tools that help you efficiently manage these "giant shoulders."

- **Why do you need a plugin manager?**
  Manual plugin installation is a nightmare. Plugin managers automate downloading, installing, updating, and uninstalling plugins, resolving dependency conflicts, and keeping your `.vimrc` clean.
- **Recommendation: `vim-plug`**
  Lightweight, fast, and asynchronous. It's one of the most popular plugin managers today.

**Practical Thinking:**
1.  **Install `vim-plug`**: (If you haven't already)
    ```bash
    curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
        https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
    ```
2.  **Configure plugins in `.vimrc`**:
    ```vim
    call plug#begin('~/.vim/plugged')

    " Fuzzy file finder, works with fzf command-line tool, an efficiency beast
    Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
    Plug 'junegunn/fzf.vim'

    " Syntax checking and formatting, makes your code more standard
    Plug 'dense-analysis/ale'

    " Auto-completion, speeds up coding
    Plug 'neoclide/coc.nvim', { 'branch': 'release' }

    call plug#end()
    ```
3.  **Install plugins**: Save `.vimrc`, reopen Vim, and execute `:PlugInstall`.

**Plugins are not a panacea, but they can greatly extend Vim's capabilities. Learning to choose and configure high-quality plugins is a necessary step to becoming a Vim master. But remember, don't be "held hostage" by plugins; the core power always lies in Vim itself.**

---

## Conclusion: Become the Master of Your Tools

We've discussed the "Dao" (Text Objects, Registers, Regular Expressions), the "Art" (Macros, Global Commands, Command-line Window, Windows and Buffers), and the "Soul" (Dancing with the Shell, Autocommands, Vimscript, Plugins) of Vim. You should now appreciate the power of its design philosophy. Vim isn't about rote memorization; it's about understanding its "language" and using it to creatively solve problems.

Become the master of your tools, not their slave. This means:

1.  **Constantly think:** "Is there a more Vim-like way to do this?"
2.  **Continuously learn:** Vim's documentation `:help` is the best teacher.
3.  **Stay curious:** Explore plugins, write your own scripts, and make Vim an extension of yourself.

Now, close those flashy IDEs, open your terminal, and begin your true cultivation.
