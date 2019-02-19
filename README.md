# context-switch
Git hooks for starting builds locally by doing git push

## Setup
1. Clone a bare local repository of a project for which you want to setup builds on push
   ```
   git clone --bare -l --no-tags source dest
   ```
2. Make the new repository be a remote of where you cloned from
   ```
   git remote add remote-name dest
   ```
3. Copy `pre-receive` and `post-receive` to `dest/hooks`
4. Create a git alias to push a branch to dest. `pre-receive` and `post-receive` use the environment variables `$BUILD_DIR` and `$BUILD_COMMAND` to find out where and how to build. `$BUILD_DIR` should point to where you want to check out and `$BUILD_COMMAND` should be the command to build. Something like this in your repository's `.git/config` will do the trick:
   ```
   [alias]
       build = "! BUILD_DIR=\"/path/to/src/build/\" BUILD_COMMAND=\"./build\" git push -f dest $2"
   ```
   
You're going to notice that to actually trigger that build you *need* the push to go through. This means that you have to make sure that the branch isn't there before you push it. This you do as usual with:
```
git push --delete dest branch-name
```
And to stifle the warnings about deleting a branch you're on you'd also have to do the following in `dest`:
```
git config receive.denyDeleteCurrent ignore
```
