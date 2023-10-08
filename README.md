# makeheaders

This is a fork of https://fossil-scm.org/home/file?name=src/makeheaders.c hacked for the purpose of adding an optional output directory for the generated headers.
Pull requests from more talented programmers than I am (a low bar) will be ~~blindly~~ gladly merged.

To give it an optional output directory, try `-o=./my_local_dir` or `-o=/some/path/to/a/dir`

Now here is my idea of the workflow, it is a bit hacky but it does save some time when set up :

Create an `inc`, `interface` and `src` directory in you project.
In `src` put all your .c files.
In `interface` put all your typedefs/structs declarations in `.h` files, do not add header guards and do not `#include` them in your `.c` files.
`makeheaders` will copy their content into automatically generated headers.

In each of your `[filename].c` files add an `#include "[filename].h`

Finally in your `~/.vimrc` add the following :
```vimscript
if !empty(glob("Makefile")) || !empty(glob("makefile")) || !empty(glob("xmake.lua")) 
	autocmd BufWritePost *.c zsh -c "makeheaders -o=./inc interface/*(N) src/*(N) inc/*(N)" 
endif
```
This will trigger the generation of header files each time you update a buffer. If no re-generation is needed `makeheaders` will not touch the generated files
and `make` won't rebuild for nothing.
This only work if you have zsh installed and in your `PATH`. The reason for using `zsh` is because of the handy `*(N)` glob syntax. There's probably a `bash`
equivalent but I can't be arsed.

This will only work if you are using vim from your project root directory. 
A good way to avoid typing `vi /[src|interface]/some_file.[ch]` each time is to use either from `zsh` to use `vi -t` and then pressing `<TAB>` will autocomplete tags from your ctags file or to just use `vi src` or `vi interface` will let you navigate to the file you want using `j` and `k` and `Enter`.

Here is the original documentation of `makeheaders` : https://fossil-scm.org/home/doc/trunk/tools/makeheaders.html
