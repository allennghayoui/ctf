# Git Forensics

## .git Folder Forensics
### Show Commit History
`git log`

### Show Metadata Of Certain Commit
`git show <commit-hash>`

## Git Repository Forensics (If the files of the repository are accessible)
### Compare File Changes Between Commits
`git diff <commit1-hash> <commit2-hash>`

### Identify Who Introduced A Change To A File
`git blame <file-name>`
