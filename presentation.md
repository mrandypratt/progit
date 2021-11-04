# What is .gitignore?
`.gitignore` is a file which stores glob patterns which indicates files which will not be committed to the repo.
Adding `node_modules` to ignore project depenency packages is the most useful case, but can be used for any directory, file, or pattern.

## General Rules
`.gitignore` in root folder will apply in all subdirectories. 
`.gitignore` files in subdirectories will override the root file.

this file follows the following rules:
- `#` for comments
- Blank lines ignored
- Standard glob patterns work
- `/` at pattern start avoids recursivity
- `/` at pattern end specifies directory
- `!` at pattern start ignores a pattern

#### Glob Patterns
Simplified RegExs like what shells use.
- `*` matches zero or more characters
- `[abc]` matches any character inside brackets
- `?` matches single character
- `[a-z]` matches any character in range
- `**` matches nested directory (at any level)


## Viewing Changes
`git diff`shows all modified but not staged changes, line by line
`-s` or `--staged` compares staged changes to last commit.
`git difftool` can be used to visualize. run with `--tool-help` to see available options.