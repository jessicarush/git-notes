# Git logs

```bash
git log > filename.txt
```

## Basic Options

`--oneline`: Displays each commit on a single line, showing the abbreviated commit hash and the commit message.

```bash
git log --oneline
```

`--graph`: Displays an ASCII graph of the branch and merge history next to the commit log.

```bash
git log --graph
```

`--decorate`: Adds branch and tag names to the commit messages.

```bash
git log --decorate
```

`--stat`: Shows the number of insertions and deletions in each commit.

```bash
git log --stat
```

`--patch` or `-p`: Shows the diff introduced in each commit.

```bash
git log -p
```

`--name-only:` Shows only the names of the files that were changed in each commit.

```bash
git log --name-only
```

`--name-status`: Shows the names of files that were changed, along with a status of whether they were added, modified, or deleted.

```bash
git log --name-status
```

## Formatting Options

`--pretty=format:"<format>"`: Allows you to format the output with placeholders like `%h` for the commit hash, `%an` for the author name, and %s for the subject.

```bash
git log --pretty=format:"%h - %an, %ar : %s"
```

`--abbrev-commit`: Shows only the first few characters of the commit hash, rather than the full 40-character hash.

```bash
git log --abbrev-commit
```

`--relative-date`: Shows the date relative to the current time (e.g., "2 weeks ago") instead of the full date.

```bash
git log --relative-date
```

## Filtering Options

`--author=<pattern>`: Filters commits by a specific author.

```bash
git log --author="John Doe"
```

`--since=<date>` and `--until=<date>`: Filters commits by date.

```bash
git log --since="2 weeks ago"
```

`--grep=<pattern>`: Searches for commits that contain a specific string in the commit message.

```bash
git log --grep="bug fix"
```

`--reverse`: Reverses the order of the commits (shows oldest first).

```bash
git log --reverse
```

## Range Options

`<commit>`: Shows commits starting from a specific commit.

```bash
git log HEAD~3
```

`<commit1>..<commit2>`: Shows commits between two commits.

```bash
git log HEAD~5..HEAD~2
```

`--merges`: Shows only merge commits.

```bash
git log --merges
```

`--no-merges`: Excludes merge commits from the log.

```bash
git log --no-merges
```

## Example Usage

Combine options to get more customized output. For instance, to view a one-line graph of commits by a specific author since a certain date:

```bash
git log --since="2023-01-01" --until="2023-12-31" --decorate > git_log_2023.txt
```