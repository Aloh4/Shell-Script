fonte:
https://askubuntu.com/questions/525767/what-does-an-exec-command-do


exec is a command with two very distinct behaviors, depending on whether at least one argument is used with it, or no argument is used at all.

If at least one argument is passed, the first one is taken as a command name and exec try to execute it as a command passing the remaining arguments, if any, to that command and managing the redirections, if any.

If the command passed as first argument doesn't exist, the current shell, not only the exec command, exits in error.

If the command exists and is executable, it replaces the current shell. That means that if exec appears in a script, the instructions following the exec call will never be executed (unless exec is itself in a subshell). exec never returns. Shell traps like "EXIT" won't get triggered either.

If no argument is passed, exec is only used to redefine the current shell file descriptors. The shell continue after the exec, unlike with the previous case, but the standard input, output, error or whatever file descriptor has been redirected take effect.

If some of the redirections uses /dev/null, any input from it will return EOF and any output to it will be discarded.

You can close file descriptors by using - as source or destination, e.g. exec <&-. Subsequent read or writes will then fail.
