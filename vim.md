# Minimal vimrc for remote sessions

``vimrc
set hlsearch    " highlight all search results
set ignorecase  " do case insensitive search 
set incsearch   " show incremental search results as you type
set number      " display line number
set noswapfile  " disable swap file
```

equivalent to:

```vimrc
set hls ic is nu noswf
```

# Align Markdown tables

Use the [easy align plugin](https://github.com/junegunn/vim-easy-align), highlight the table, then `:'<,'>:EasyAlign<CR>` then, `\*|<CR>`

# Macro over visual selection

Macro over visual selection for macro recorded in e.g. `q` register

```:'<,'>normal @q```

# Check variable

```:echo g:variable_name```
