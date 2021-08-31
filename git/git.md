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

## (Chaning remote repo](<https://stackoverflow.com/questions/2432764/how-to-change-the-uri-url-for-a-remote-git-repository>)

```bash
git remote set-url origin new.git.url/here
```

## Line endings

- these can be annoying to manage in git, but there are facilities exposed to you as good 'ole [Scotty](https://www.hanselman.com/blog/carriage-returns-and-line-feeds-will-ultimately-bite-you-some-git-tips) shows us

## Get default branch

```bash
git symbolic-ref -q HEAD | sed 's@^refs/heads/@@'
```

## SSH keys

- [generating ssh keys and adding them to github](https://docs.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [setting up ssh on windows for aws codecommit](https://docs.aws.amazon.com/codecommit/latest/userguide/setting-up-ssh-windows.html)
  - [setting up for multiple accounts](https://gist.github.com/justinpawela/3a7056cd592d688425e59de2ef6f1da0)
  - [setting up for multiple accounts using local git config](https://hands-on.cloud/how-to-manage-multiple-codecommit-repositories-from-the-single-machine/)

## Tools

- [common commands you don't know](https://dangitgit.com/en)
