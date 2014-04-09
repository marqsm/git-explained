GIT
=====

## What
- Saves snapshots of a folder (or directory tree).
- Actually saves everything (when looked from user perspective)
    * but packs it by getting deltas
    * For most situations you can think of commits as containing the whole tree <br>(the commits connection to its parents makes sure it has the data).
    * ...BUT for some situations where you detach the commit from its parent (like rebase or cherry-pick), thinking of it as "changes" is easier.
- Kind of like a key-value store, key (commit name / commit)

- Everything is local
    -> De-centralized
    -> remotes are basically just branches with a remote address
- Branches basically just the same as copying the whole folder at some point in time


## So...
- Repository always has the whole history.
- Merge is a magical glue that enables "communication" between snapshots
    -> when we cherry-pick a commit, we just merge to directories.
    -> That's why you don't need to worry about getting older commits
- Almost never loses anything (altough commits that aren't connected to anything can be a hassle to find) - see `git reflog`


## How
- Connects snapshots (commits) to their parents
- Have a pointer to current working tree (HEAD)
- Everything (almost) creates a new commit
    - except rebase
    - except if you do `git reset --hard HEAD`
    - See `git reflog` to see what detailed history


## How in some more detail
- what's in a commit?
    * `git log --oneline --decorate`
    * `git show c1a881f`
        * Name (key) - SHA1 hash (of timestamp, author, commit message)
        * Author
        * Message
    * `git cat-file -p c1a881f`
- Has
    * Tree
    * Parent (except repo first commit, and "lost" commits)
    * Author
    * Commiter
    * Message


## Merges
"A merge creates a new commit that incorporates changes from other commits. Before merging, the stage must match the current commit. "
If the commits share an ancestor, it's a "fast forward merge"


## Reverting to older version
`git checkout <hash>` moves HEAD to that point in time (or commit - anyway meaning you won't see the newer commits in log)

`git revert <hash/HEAD~n>` will take the old commit, and copy it as a new commit to HEAD<br>
NOTE: If you edited same files as the that commit, you'll have to merge the differences.

[More advice on undoing commits](http://lostechies.com/jasonmeridth/2010/03/23/git-reset-checkout-amp-quot-bare-double-dash-quot-and-revert/)

## .git
`.git/config` - repository config file
`./git/HEAD` where the HEAD is currently pointing (what's in your working directory)<br>
`./git/refs/heads/<branch-name>` name of the reference to head of branches <br>
`./git/refs/tags/` tags (name = tag name, content = commit-name)<br>
`.git/info/exclude` - like ignore, but local<br>
`.git/info/refs` - connect tags to commits, but local<br>
`.git/hooks` - you can make automatic actions connected to git-commands.<br>
`.git/logs` - keeping the logs for each branch in `.git/logs/refs/heads/`


## Cherry-pick
`git cherry-pick <commit-id-or-tag>`
- Just takes an existing commit, inserts it as a new commit to HEAD (-> also gets a new name/id)
- Because the new commit has a different parent than the original, just the changes are applied
- In a way, the opposite of revert


- Means if you merge later, that change is in a way applied in two different commits.<br>
So for posterity, merge is simpler, but if merge can't be used is a good tool.
- If you use a tag-name, it just takes the latest commit, as the parent reference is changed.
- If you don't want git to commit the change automatically, use `git cherry-pick --no-commit <commit-id-or-tag>`
- IMPORTANT: If you cherry-pick multiple commits, remember to start with the oldest.
Otherwise you might end up with making the commits in an impossible order
or create conflicts where there shouldn't be any.


## Change commit message
Say you typoed a commit message
`git commit --amend`


## Rebase
Changing history (actual commits)
- Powerful
- Should only be used on commits you haven't shared (commit id:s change, some commits actually get deleted and this can be a problem if working with remotes)
- That being said, Rebase can be very useful
    - Squashing
        Easiest I know is `git rebase -i HEAD~3` where you can choose what to do.


## Moving & renaming files
Git actually just removes and adds them in both cases

BUT implicitly, if contents are not changed on that commit, it has the same hash (and thus can be recognized as "just a move/rename")


## Nice commands
`git add *.php` Adds php-files in current folder <br>
`git add '*.php'` Adds php-files from all subfolders<br>
`git add -p` add in patch-mode, meaning you can choose which parts of the file you want to commit.

`git commit -am "my asdfcommit"` add changed and commit <br>
`git commit --amend` change that typoed commit message

`git log --oneline --decorate` see nicer list of commits <br>
`git log --graph --oneline` get a nice log tree, see when branch merges have happened <br>
`git log dev..my-dev --oneline` see commits that are in `my-dev` that aren't in `dev`

`git remote show origin` get the address where remote is pointing


## Nice configurations to make your life easier

`git config --global alias.unstage 'reset HEAD'` - nicer unstaging with `git unstage README`

`git config --global alias.tree 'log --graph --decorate --pretty=oneline --abbrev-commit'`

`git config --global color.ui=auto` Get some colours

`git.config --global core.editor /path/to/editor` Choose the editor (nice especially when `git rebase -i`)


## More info
The book - "Pragmatic guide to Git". Short, one opening explains one situation or command.

[Git parable](http://tom.preston-werner.com/2009/05/19/the-git-parable.html) to get the mental model (or "what it really does")

[Git is simpler than you think](http://nfarina.com/post/9868516270/git-is-simpler)

[Understanding Git](http://www.sbf5.com/~cduan/technical/git/git-1.shtml)

[Wikipedia](http://en.wikipedia.org/wiki/Git_(software))

[Simple interactive tutorial(commits, branches etc - I learned something new)](http://try.github.io/levels/1/challenges/1)

[Interactive "Learn Git Branching" (Includes plenty of stuff)](http://pcottle.github.io/learnGitBranching/)

[When to use different merge strategies](http://stackoverflow.com/questions/366860/when-would-you-use-the-different-git-merge-strategies)

[Lots of git tips](http://alblue.bandlem.com/Tag/git/)