# pipex

A 42 project that recreates the shell's pipe (`|`) operator in C, using `pipe()`, `fork()`, `dup2()`, `execve()`, and `wait()`.

It implements the equivalent of:

```bash
< infile cmd1 | cmd2 > outfile
```

…using only system calls.

## How it works

1. Open `infile` for reading and `outfile` for writing
2. Create a pipe with `pipe()`
3. `fork()` a child for `cmd1`: redirect stdin from `infile`, stdout to the pipe write-end, then `execve()` the command
4. `fork()` a child for `cmd2`: redirect stdin from the pipe read-end, stdout to `outfile`, then `execve()`
5. Parent closes pipe FDs and `wait()`s for both children

## Build

```bash
make
```

## Run

```bash
./pipex infile "cmd1" "cmd2" outfile
```

Example:

```bash
./pipex input.txt "grep hello" "wc -l" output.txt
```

…is equivalent to `< input.txt grep hello | wc -l > output.txt`.

```bash
$ echo "Hello world" > infile.txt
$ ./pipex infile.txt "cat" "wc -w" outfile.txt
$ cat outfile.txt
2
```

## Layout

- `pipex.c`, `pipex.h` — entry point + headers
- `ft_split.c`, `ft_strjoin.c`, `ft_strlen.c`, `ft_substr.c` — libft helpers used to resolve commands against `$PATH`
- `Makefile`
