# wizard.el - A Lightweight Suite of Essential Emacs Functions
![Build Status](https://github.com/jamescherti/wizard.el/actions/workflows/ci.yml/badge.svg)
![License](https://img.shields.io/github/license/jamescherti/wizard.el)
![](https://jamescherti.com/misc/made-for-gnu-emacs.svg)

The **wizard** Emacs package provides a curated collection of quality-of-life enhancements for your daily editing workflow.

The philosophy behind this package is simple: **leverage the robust APIs Emacs already provides.** Instead of rewriting complex logic from scratch or adding heavy external dependencies, **wizard** intelligently wraps and extends built-in Emacs functions to deliver practical features.

## Features Overview

### Visuals & Highlighting
- **Symbol highlight:** `(wizard-toggle-highlight-at-point)` toggles highlighting for the symbol under your cursor or the active selection. It is a lightweight alternative to the *highlight-symbol* package.
- **Native TODO highlighting:** `(wizard-hl-todo-mode 1)` automatically highlights codetags **TODO, FIXME, BUG, NOTE**, etc. in your buffers using custom font-lock rules. This mode is an alternative to the *hl-todo* package that uses Emacs built-in `hi-lock`.

### Buffer & State Management
- **True hard reset:** `(wizard-reload-current-buffer)` completely reloads the current buffer from disk. Unlike the standard `revert-buffer`, this command kills and reopens the file, instantly purging broken states, glitched minor modes, or out-of-sync LSP servers. It silently saves your work first and takes an exact snapshot of your window coordinates to restore your point, window start, and pixel-exact scroll position. Your layout will not shift by a single pixel.
- **Enhanced buffer cloning:** `(wizard-clone-indirect-buffer)` and `(wizard-clone-and-switch-to-indirect-buffer)` improve the built-in clone function. They preserve your point position, window start, and horizontal scroll. Evil users also benefit, as these functions prevent Evil mode search highlights from bleeding into newly cloned indirect buffers. Switch back easily with `(wizard-switch-to-base-buffer)`.

### Smart Text Manipulation
- **Paste with matching indentation:** `(wizard-paste-indented)` pastes text from your clipboard while automatically matching the current line's indentation. It removes the minimal common leading whitespace from the clipboard content and aligns it perfectly with your cursor.
- **Copy unindented text:** `(wizard-copy-unindented)` copies the active region while stripping away minimal common leading whitespace. If no region is active, it copies the current line without its leading indentation.
- **Shift regions easily:** `(wizard-move-region-up)` and `(wizard-move-region-down)` move the currently selected text up or down. These commands maintain the active selection after moving and support both standard Emacs regions and Evil mode visual states.

### Search & Replace
- **Noise-free Grep:** `(wizard-grep)` runs Grep with a clean, empty prompt. Unlike the built-in `M-x grep` which pre-fills the minibuffer with the entire shell command, this command lets you type only your search arguments. The base command is prepended automatically, reducing visual noise and preventing accidental edits to your flags.
- **Quick symbol replacement:** `(wizard-replace-symbol-at-point)` lets you instantly replace all occurrences of the symbol under your cursor with a specified string.

### Code Navigation
- **Indentation jumping:** `(wizard-point-backward-to-lower-indentation)` and `(wizard-point-forward-to-lower-indentation)` move your point to the nearest line with a lower indentation level, making it easy to navigate out of nested code blocks.
- **Empty line jumping:** `(wizard-point-backward-to-empty-line)` and `(wizard-point-forward-to-empty-line)` quickly jump to the nearest blank line.
- **Ignore invisible text:** Setting `wizard-point-ignore-invisible` to a non-nil value allows these navigation commands to skip folded or hidden text.

## Installation

### Install with straight (Emacs version < 30)

To install `wizard` with `straight.el`:

1. It if hasn't already been done, [add the straight.el bootstrap code](https://github.com/radian-software/straight.el?tab=readme-ov-file#getting-started) to your init file.
2. Add the following code to the Emacs init file:
```emacs-lisp
(use-package wizard
  :straight (wizard
             :type git
             :host github
             :repo "jamescherti/wizard.el"))
```

### Installing with use-package and :vc (Emacs version >= 30)

To install `wizard` using the built-in `:vc` keyword:

```emacs-lisp
(use-package wizard
  :vc (:url "https://github.com/jamescherti/wizard.el"
       :rev :newest))
```

## Customizations

### Codetags highlighting

You can customize the codetags that get highlighted by modifying the `wizard-hl-todo-keywords` variable. By default, it highlights `TODO`, `FIXME`, `BUG`, `XXX` (with a warning face, generally red) and `NOTE`, `HACK`, `DONE` (with a doc face, generally green).

To enable this feature globally, add the following to your Emacs configuration:

```elisp
(wizard-hl-todo-mode 1)
```

This mode is an alternative to the *hl-todo* package that uses Emacs built-in `hi-lock`.

### Case sensitivity

The functions `(wizard-toggle-highlight-at-point)` and `(wizard-replace-symbol-at-point)` depend on built-in functions that can be customized via the following variables:

- `case-fold-search`: This buffer-local variable determines the behavior of `(wizard-toggle-highlight-at-point)` and `(wizard-replace-symbol-at-point)`. When set to t (default), both symbol highlighting and searches become case-insensitive, matching symbols regardless of case. When set to nil, they become case-sensitive, matching symbols only when the case exactly matches the text in the buffer.
  Example:
  ```elisp
  ;; Setting case-fold-search to nil enables case-sensitive symbol highlighting
  ;; and search/replace
  (setq-default case-fold-search nil)
  ```

- `case-replace`: When non-nil, this variable ensures that `(wizard-replace-symbol-at-point)` preserves the case of the original text during replacements.
  Example:
  ```elisp
  ;; t means (wizard-replace-symbol-at-point) should preserve case in
  ;; replacements.
  (setq case-replace t)
  ```

### Making wizard-grep use rg (ripgrep) instead of grep

To configure `(wizard-grep)` to use ripgrep, add the following snippet to your init file:

```elisp
(setq grep-use-null-device nil)
(setq grep-command-position nil)
(setq grep-command
      (concat "rg"
              ;; Include hidden files
              " --hidden"
              ;; Exclude VC
              " -g !.git -g !.svn -g !.hg"
              ;; Default
              " --null --line-buffered --color=never --max-columns=1000"
              " --path-separator / --smart-case --no-heading"
              " --with-filename --line-number --search-zip"))

;; Synchronize grep-template with the custom ripgrep command defined above.
;; The <R> and <F> tokens serve as dynamic placeholders that Emacs replaces
;; with the search regular expression and target file patterns at runtime.
(setq grep-template (concat grep-command " <R> <F>"))
```

## Author and License

The `wizard` Emacs package has been written by [James Cherti](https://www.jamescherti.com/) and is distributed under terms of the GNU General Public License version 3, or, at your choice, any later version.

Copyright (C) 2024-2026 James Cherti

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version. This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program.

## Links

- [wizard.el @GitHub](https://github.com/jamescherti/wizard.el)

Other Emacs packages by the same author:
- [compile-angel.el](https://github.com/jamescherti/compile-angel.el): **Speed up Emacs!** This package guarantees that all .el files are both byte-compiled and native-compiled, which significantly speeds up Emacs.
- [outline-indent.el](https://github.com/jamescherti/outline-indent.el): An Emacs package that provides a minor mode that enables code folding and outlining based on indentation levels for various indentation-based text files, such as YAML, Python, and other indented text files.
- [easysession.el](https://github.com/jamescherti/easysession.el): Easysession is lightweight Emacs session manager that can persist and restore file editing buffers, indirect buffers/clones, Dired buffers, the tab-bar, and the Emacs frames (with or without the Emacs frames size, width, and height).
- [vim-tab-bar.el](https://github.com/jamescherti/vim-tab-bar.el): Make the Emacs tab-bar Look Like Vim's Tab Bar.
- [elispcomp](https://github.com/jamescherti/elispcomp): A command line tool that allows compiling Elisp code directly from the terminal or from a shell script. It facilitates the generation of optimized .elc (byte-compiled) and .eln (native-compiled) files.
- [tomorrow-night-deepblue-theme.el](https://github.com/jamescherti/tomorrow-night-deepblue-theme.el): The Tomorrow Night Deepblue Emacs theme is a beautiful deep blue variant of the Tomorrow Night theme, which is renowned for its elegant color palette that is pleasing to the eyes. It features a deep blue background color that creates a calming atmosphere. The theme is also a great choice for those who miss the blue themes that were trendy a few years ago.
- [Ultyas](https://github.com/jamescherti/ultyas/): A command-line tool designed to simplify the process of converting code snippets from UltiSnips to YASnippet format.
- [dir-config.el](https://github.com/jamescherti/dir-config.el): Automatically find and evaluate .dir-config.el Elisp files to configure directory-specific settings.
- [flymake-bashate.el](https://github.com/jamescherti/flymake-bashate.el): A package that provides a Flymake backend for the bashate Bash script style checker.
- [flymake-ansible-lint.el](https://github.com/jamescherti/flymake-ansible-lint.el): An Emacs package that offers a Flymake backend for ansible-lint.
- [inhibit-mouse.el](https://github.com/jamescherti/inhibit-mouse.el): A package that disables mouse input in Emacs, offering a simpler and faster alternative to the disable-mouse package.
- [quick-sdcv.el](https://github.com/jamescherti/quick-sdcv.el): This package enables Emacs to function as an offline dictionary by using the sdcv command-line tool directly within Emacs.
- [enhanced-evil-paredit.el](https://github.com/jamescherti/enhanced-evil-paredit.el): An Emacs package that prevents parenthesis imbalance when using *evil-mode* with *paredit*. It intercepts *evil-mode* commands such as delete, change, and paste, blocking their execution if they would break the parenthetical structure.
- [stripspace.el](https://github.com/jamescherti/stripspace.el): Ensure Emacs Automatically removes trailing whitespace before saving a buffer, with an option to preserve the cursor column.
- [persist-text-scale.el](https://github.com/jamescherti/persist-text-scale.el): Ensure that all adjustments made with text-scale-increase and text-scale-decrease are persisted and restored across sessions.
- [pathaction.el](https://github.com/jamescherti/pathaction.el): Execute the pathaction command-line tool from Emacs. The pathaction command-line tool enables the execution of specific commands on targeted files or directories. Its key advantage lies in its flexibility, allowing users to handle various types of files simply by passing the file or directory as an argument to the pathaction tool. The tool uses a .pathaction.yaml rule-set file to determine which command to execute. Additionally, Jinja2 templating can be employed in the rule-set file to further customize the commands.
- [kirigami.el](https://github.com/jamescherti/kirigami.el): The *kirigami* Emacs package offers a unified interface for opening and closing folds across a diverse set of major and minor modes in Emacs, including `outline-mode`, `outline-minor-mode`, `outline-indent-minor-mode`, `org-mode`, `markdown-mode`, `vdiff-mode`, `vdiff-3way-mode`, `hs-minor-mode`, `hide-ifdef-mode`, `origami-mode`, `yafolding-mode`, `folding-mode`, and `treesit-fold-mode`. With Kirigami, folding key bindings only need to be configured **once**. After that, the same keys work consistently across all supported major and minor modes, providing a unified and predictable folding experience.
- [buffer-guardian.el](https://github.com/jamescherti/buffer-guardian.el): Automatically saves Emacs buffers without requiring manual intervention. By default, it triggers a save when the user switches to another buffer, switches to another window or frame, Emacs loses focus, or the minibuffer is opened. Beyond standard file buffers, *buffer-guardian* also manages specialized editing buffers such as *org-src* and *edit-indirect*. Additional features, disabled by default, include periodic or idle-time saving of all buffers, automatic exclusion of remote, nonexistent, or large files, and support for custom exclusion rules via regular expressions or predicate functions.
