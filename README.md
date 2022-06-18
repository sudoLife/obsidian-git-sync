# How to sync Obsidian notes with git while __also__ preserving timestamps

Okay, so this problem arose from me trying to use my VPS to backup my digital brain.

It works fine, however when I cloned the repo to another machine, I discovered all _mtime_ timestamps were messed up. And that something I rely heavily on.

So here's a solution to make it work.

This manual supposes you know how to use git, are acquainted with a terminal and use Linux. Gotta include a broader audience later.

## Prerequisites
- git
- a repository to sync with (on a VPS or any github/gitlab hostings)
- your digital brain

## Initialization

__BACKUP YOUR VAULT IN CASE YOU OR I MESS UP!__

First, you have to initialize your repository. Open terminal, go to your Obsidian folder and run `git init`.
You can add a `.gitignore` file to exclude some of your workspace files. 
I prefer to keep them synchronized with the exception of `.obsidian/workspace`, because it's too annoying.

__Hooks installation: you will have to do this on all computers you use Obsidian on.__
Next, go to [hooks](https://github.com/danny0838/git-store-meta), download `git-store-meta.pl` and place it into your `.git/hooks`.
Then make it executable using `chmod +x <path to the hooks file>`.

Finally, to automatically store and apply metadata, run `.git/hooks/git-store-meta.pl --install`. This will create all necessary hooks.

The rest is history: set your upstream url, add all files, and push!
The convenient way is to run the following script (replace `<placeholders>`  with your values):

```bash
#!/usr/bin/env bash
ZK_PATH="<full path to your obsidian vault folder>"

printf "Syncing %s...\n" $ZK_PATH

cd $ZK_PATH

git pull

CHANGES_EXIST="$(git status --porcelain | wc -l)"

if [ "$CHANGES_EXIST" -eq 0 ]
then
    echo "No changes in the local repository, exiting..."
    exit 0
fi

git add .
git commit -m "Last Sync: $(date +"%Y-%m-%d %H:%M:%S")"
git push
```

The idea behind the script is taken from [this article](https://medium.com/analytics-vidhya/how-i-put-my-mind-under-version-control-24caea37b8a5).

## TODOs
- [ ] [automate](https://rjzaworski.com/2018/01/keeping-git-hooks-in-sync) installing git hooks.
- [ ] Guide for Windows?


