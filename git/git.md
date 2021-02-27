# GIT

## Set up git credential manager
```bash
git config --global credential.helper "/mnt/c/Program\ Files/Git/mingw64/libexec/git-core/git-credential-manager.exe"
```

## Rename branch
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

## Line endings
- these can be annoying to manage in git, but there are facilities exposed to you as good 'ole [Scotty](https://www.hanselman.com/blog/carriage-returns-and-line-feeds-will-ultimately-bite-you-some-git-tips) shows us
