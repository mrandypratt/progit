Chapter Summaries:
1. Getting Started (8): VCS and Git non-technical summary
2. Git Basics (24): Git usage (clone, view history, modify files, contribute changes)
3. Git Branching (63): Branching model
4. Git on the Server (105): Collaboration
5. Distributed Git (126): multiple remote repos, git ove email, juggling remote branches and contributed patches
6. Github (167): Sign-up & manage account, workflows, interface
7. Git Tools (220): reset, binary search, master level git
8. Customizing Git (343): hook scripts & commit policies
9. Git and Other Systems (375): VCS conversions (from SVN)
10. Git Internals (431): object model, packfiles, server protocols, etc.

# Chapter 1: Getting Started

## Version Control Systems
Version Control System (VCS): allows one to revert selected files to a previous state, compare changes over time, track who made the changes, and recover lost files/data.
Revision Control System (RCS): early Local Verison Control implementation which keeps patch sets (differences between files in a special format)
Centralized VCS: Shared repository for all developers.
Pros: Everyone has access to the main files and control over who makes changes.
Cons: Single point of Failure.
Distributed VCS: Full copy of Repo is on central repo as well as each client (Developer).
- This includes the version history, not just the current snapshot.
- Multiple workflows are enabled due to independent client repositories.

## How Git Works

### Snapshots
As opposed to other systems (CVS, SVN) which are delta-based (only tracking changes)
Git Snapshots each file and stores a reference to the snapshot
- If a file hasn't changed, git just passes the reference forward instead of taking a new snapshot.
- In this way, git is more of a mini filesystem

### Local Operations
All communication with remote repo is done when cloning and when pushing commits. 
- This avoids latency when communitcation with master repo
- This also avoids changes being made while working on projects.
- You can work with no connection to the remote server (VPN or Internet)

### Integrity
Data is Checksummed using SHA-1 Hash based on file contents or directory structure.

### Deleting is Difficult
Almost every action adds to Git database and makes undoing or erasing commit history near impossible, especially if you push your changes to remote repo.

### **The Three States**
Files are always in one of the following states:
- *modified*: Changes have not been committed to database
- *staged*: Changes have been marked to go into next commit
- *committed*: Data is safely stored in local database

Additionally, there are three main sections of Git Projects:
- Working Tree: Files pulled from compressed database in Git directory an dplaced on disk for use and modification. 
- Staging Area (Index): A file which stores info about what goes into your next commit.
- .git directory (repo): Where Git stores metadata and object database for project.
  - This is what is coped when a repo is cloned.

The Workflow:
1. Modify files in the working tree (modified)
2. Selectively stave changes for next commit (staged)
3. Commit changes from staging area into permanent Git directory (committed)

## Customizing Git

### `git config`
Using `git config`, you can set configuration variables in one of three places:
1. `[path]/etc/gitconfig`: Applied to every used on system and each repository in them.  To use, you will need admin/super privilege and the `--system` option.
2. `~/.gitconfig`/`~/.config/git/config`: Specific to user modified using `--global`
3. `./.git/config`: Git repo specific, which is the default or specifically selected using `--local`. Must be run inside the repo.

Each level overrides the previous level: `--system` settings overriden in `--global` settings overridden in `--local`.

### Identity:
Set your User name and Email Address
```
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

### Default Branch
Name defaults to *master* with `git init`
Change with `$ git config --global init.defaultBranch <prefname>` command.

### Settings
`git config --list`: List of settings
`git config --show-origin <keyname>`: shows config file location

### Git Help
For Manual Pages:
```
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
```
For Quick Help:
`git <verb> -h`

# Chapter 2: Git Basics

## Getting a Git Repo
1. Create one locally (Turn local directlry into git repo)
2. Clone an existing repo (Clone remote repo locally)

### Initializing from Existing (Local)
From Project Root Directory: `git init`
- creates `.git` subdirectory with necessary repo files

### Cloning Existing Repo
`git clone <url> <newdirname>` copies full repository, including history.
Not checkout, which was used for a working copy.
This command:
- Initializes directory with project root dir name (or `<newdirname>` if the optional argument is provided)
- initializes .git directory
- Pulls all data for repo
- checks out working copy of latest version

#### Transfer Protocols
Git repos can be cloned using `https://`, `git://`, or `user@server:path/to/repo.git` using SSH.

## Recording Changes
`git add <fileordirname>` will stage modified files for future commits

### Tracked vs Untracked
- Tracked Files: Files committed (unmodified) or added (staged) to the next commit (basically any file git is aware of)
- Untracked files: Modified and unstaged files

### `git status`
command which shows the state of files in the repo
- Untracked Files - Untracked Modified files: not added to staging or committed
- Changes to be committed: files `add`ed to next commit
  - Classifies as new file or modified
- Changes not staged for commit: Tracked and modified files
For a less wordy status, use `-s` or `--short`
- Untracked => `??`
- Staged/Added => `A`
- Modified => `M`
Left Column is for staging area (changes from last snapshot)
Right column is for working tree (changes since last staging)

### Ignoring files
`.gitignore` stores patterns by which to ignore files
This can be in root directory and in any subdirectory as well
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

## Committing
`git commit` by itself will launch your text editor
- `EDITOR` shell envioronment variable determines which editor.
- `git config --global core.editor` will allow you to change this.
`-v` option adds diff information in editor
`-m` allows message to be passed inline
`-a` commits all files without requiring staging

## Removing Files
`git rm <filename>` removes file from working tree and 
- `-f` option forces removal when file is in staging area
- `--cached` option removes from staging area, but leaves working tree alone.
- Glob patterns, files, and directories can be passed as an argument (`*` must be escaped `\`)

## Moving Files
Git doesn't explicitly detect file movement or renaming.
`git mv` will change name and update in staging area.

## Viewing Commit History
`git log` => lists commits with most recent at the top
- `-p` or `--patch` shows the differences of each commit
- `-2` or any number will limit number of entries displayed
- `--stat` option shows things like files/lines added/inserted/deleted
- `--pretty=<arg>` where args are listed below. Changes display options
  - `short`, `full`, `fuller`, `oneline`, `format`
  - `format` allows you to specify elements to include in log:
    - hashes, author information, commit date, subject
- `--graph` Particularly useful with with `format` and `oneline`. Used for seeing branching
More Complete list of `git log` options
- `-p` => Show the patch introduced with each commit.--statShow statistics for files modified in each commit.
- `--shortstat` => Display only the changed/insertions/deletions line from the --stat command.
- `--name-only` => Show the list of files modified after the commit information.
- `--name-status` => Show the list of files affected with added/modified/deleted information as well.
- `--abbrev-commit` => Show only the first few characters of the SHA-1 checksum instead of all 40.
- `--relative-date` => Display the date in a relative format (for example, “2 weeks ago”) instead ofusing the full date format.
- `--graph` => Display an ASCII graph of the branch and merge history beside the log output.
- `--pretty` => Show commits in an alternate format. Option values include oneline, short,full, fuller, and format (where you specify your own format).
- `--oneline` => Shorthand for --pretty=oneline --abbrev-commit used together.
- `--since` and `--until` allows you to filter by time
- `--grep` search for keywords in commit messages
- `--author`
- `-S` git "pickaxe" accepts string argument and returns commits which changed number of occurances of the string
- `git log -- path/to/file`

## Undoing things
- `git commit --amend` => replaces your last commit by adding all changes from last commit to staging area (with any changes currently in staging area) and commits the staging area again reusing the original commit message.
- `git restore --staged <file>` => unstages file
  - Prior to version 2.23.0 `git reset HEAD <file>` => unstages staged file
- `git restore <file>` => discards changes **Permanently removes changes**
  - Prior to version 2.23.0 `git checkout -- <file>...` => discards changes in working directory

## Working with Remotes
`git remote` will show each remote handle specified. Will show `origin`, the default name Git gives server cloned from
- `-v` option shows urls stored for shortname

### Adding remote repos
`git clone` implicitly adds `origin` remote for you
`git remote add <name> <URL>` is how to add repo with a new name

### Fetch and Pull
`git fetch <remote>` pulls down all remote data not locally up to date.
- This data is downloaded, but must be manually merged into your work when ready
`git pull` downloads and automatically merges remote branch into your current branch
- This is best when your branch is up to date with remote branch.
- `git clone` automatically sets local `master` branch to track remote `master` branch

### Pushing
`git push <remote> <branch>`
- If someone else has pushed since you cloned, you will have to fetch and incorporate locally before pushing
- YOu can check information using `git remote show <remote>` to check on the status

### Renaming/Removing Remotes
`git remote rename <current> <new>`
`git remote remove <remote`

## Tagging
Tags are useful for marking release points
`git tag` will list tags
- narrow search with `"search"` where search is a string. Supports `*` if `-l` option is passed.

### Tag Types
Lightweight tag: pointer to a specific commit
Annotated tags are checksummed objects with tagger name, email, dage, message, and can be verified.
- `git tag -a <tag> -m <message>` creates tag 
- `git show <tag>` will show data of commits using tag.
- `git tag -a <tag> <checksum>`: run `git log` to view checksum and add a tag to existing commit using a portion of the checksum
- `git push origin <tagname>` used to push tags, as they are not automatically pushed to remote
- `git push origin --tags` will transfer all tags to remote
- `git tag -d <tagname>` will delete tags locally
- `git push origin --delete <tagname>` will delete remote tag

## Git Aliases
`git config --global alias.<alias> <command>` will allow you to shorten your work.

PDF page 69




# Chapter 10: Git Internals

## `.git` directory 
`.git` directory contains everything you need to back up or clone your repo
Porcelean v plumbing: the user-focused commands vs under the hood commands.

### Layout
- `config`: Project-specific configuration
- `description`: Used by Gitweb - a Perl written CGI Script which allows browsing git repos in web browsers.
- `HEAD`: Discussed Below
- `hooks/`: Contains client- or server-side hook scripts
- `info/`: keeps a global exclude file for ignored patterns you don't want in `.gitignore`
- `objects/`: Discussed Below
- `refs/`: Discussed Below
- `index`: Discussed Below
- `branches/`: not discussed

## Git Objects:
Git is a *content-addressable filesystem*. This means that Git is a key-value data store.

`objects/pack`
`objects/info`
