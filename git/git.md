# GIT

## Set up git credential manager
```bash
git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/libexec/git-core/git-credential-manager.exe"
```

## rename branch
- locally
    ```bash
    # make sure you are on the branch you want to rename
    git branch -m new-name
    ```
- remote
    ```bash
    # make sure you are on the branch you want to rename
    git push -u origin new-name
    ```
