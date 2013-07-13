# notes about git

*Scenario*: I create a feature branch at the beginning of my sprint off of a fresh copy of develop.  I'm workin, workin, workin and it only turns out, I modified three files, no big deal.  Now, I need to merge my feature branch back into develop.  Ok, let me just run:

    (feature-branch) $ git checkout develop
    (develop) $ git pull origin develop 

Then a nice big commit message saying `Merge branch 'develop' into 'develop'` but now I've inexplicably modified 258 files.  WHAT THE DUECE?!?!

*Solution*: Don't just `pull` a branch into itself otherwise there will be many duplicate commits which makes the history harder to read.  Instead, you should _almost_ always use the `--rebase` flag when doing this operation.  This essentially tells Git that you just want add the latest additions from the remote server to your local branch, update your local branch to point to the latest HEAD commit (aka `fast-forward`ing) and put any of your latest changes back into the flow of the commit history accordingly.  By taking this strategy, these large, duplicate merge commits can be avoided and help keep the already massive history a little less dirty and easier to decipher.

*Scenario*: I merge develop into my feature branch.  Ah crap, merge conflict (possibly call someone over to help resolve stuff.)  Ok, great, everything is ok.  Now I do more work, more work, and then more work.  Ah crap again...I need to merge develop back in but I know I'm going to get the same merge conflicts as before...so lather, rinse, repeat.

*Solution*: Throughout my workflow, I have come across the above scenario too many times to even remember now and each time I say the same thing to myself - "What the shit?  Didn't I just do this earlier today?  Oh well, I'll just have to remember which to accept."  Wouldn't it be great if Git could remember how to solve this merge conflict?  Well, it seems that there is a hidden gem within Git that does just this - [`git rerere`](http://git-scm.com/2010/03/08/rerere.html).  A rather unfortunate sounding command (well, if you're into poop humor like myself), it sounds like the way the toilets look in the Men's room at 1stdibs after a free Wednesday lunch from Chipotle, when in actuality, it stands for "reuse recorded resolution."  This bowel movement command allows you to tell git to "remember how you've resolved a hunk conflict" so when that same conflict occurs again, like in the scenario above, it will automatically handle it for you and save you the hassle from going through that pain in the ass process multiple times.  

In order to enable this feature, a config setting must be turned on first:

    $ git config --global rerere.enabled true

Then as merge conflicts occur, git will keep track of these resolved conflicts, essentially as patterns to match against, and apply previously resolved conflicts accordingly.  There are several other commands that the link above goes into greater detail with so it's better to take a quick read over that post.

*Scenario*: I have this one little bug to fix.  Not a big deal.  I'll just comment out this line of code and commit it.  Oh, I should have checked it before committing because it's still broken.  Ok, well, let me take out some whitespace and commit that.  _Ah, I gotta go use the bathroom..._ (20 minutes later).  Oh, there's another issue that's kind of related to this first one?  Okie dokie, not a problem.  Fix.  Commit. _I'll just give it the same commit message. No biggie_.  Then soon enough, three commits balloon into 10 with the same commit message.  Oh well, push it and ship it.  No need to worry about history readability.

*Solution*: Though it may seem trivial at the time, this can definitely occur many times throughout the entire repository lifespan, especially with many developers with their hand in the cookie jar.  If there are many related commits that are in a literal lump of commits, use an interactive rebase aka squashing commits.  
The typical workflow for this would be 

1. Figure out how many commits you want to put into one
2. Use git's interactive rebase mode
3. Save, and create one commit message

In Git land:

    (develop) $ git log -n 10 # just ensure this is how many you want to combine into one
    (develop) $ git rebase -i HEAD~10

Next, you may see something like this:

    1 pick 510273d finally fixed bug
    2 pick 5c76653 updating pointer
    3 pick 5029b05 updating pointer
    4 pick 3765a56 updating pointer
    5 pick 6536c57 updating pointer
    6 pick 15073d2 updating pointer
    7 pick 5376c65 updating pointer
    8 pick 251d307 updating pointer
    9 pick 7536a65 updating pointer
    10 pick 5376c65 updating pointer
    11 # Rebase 8b819fe..5376c65 onto 8b819fe
    12 #
    13 # Commands:
    14 #  p, pick = use commit
    15 #  r, reword = use commit, but edit the commit message
    16 #  e, edit = use commit, but stop for amending
    17 #  s, squash = use commit, but meld into previous commit
    18 #  f, fixup = like "squash", but discard this commit's log message
    19 #  x, exec = run command (the rest of the line) using shell
    20 #
    21 # These lines can be re-ordered; they are executed from top to bottom.
    22 #
    23 # If you remove a line here THAT COMMIT WILL BE LOST.
    24 # However, if you remove everything, the rebase will be aborted.
    25 #
    26 # Note that empty commits are commented out

You can now edit those 10 commits to be dropped into one. The `Line 1` commit will always have `pick` next to it.  For every subsequent commit, change `pick` to `squash`.  This will tell git to combine every one of those commits into the `Line 1` commit.  Save and close the commit message.  Now, a second screen will open up with a message similar to this:

    1 # This is a combination of 10 commits.
    2 # The first commit's message is:
    3 
    4 finally fixed bug
    5 
    6 # This is the 2nd commit message:
    7
    8 updating pointer
    9 

    ... until all the commit messages are list out

    40 # Please enter the commit message for your changes. Lines starting
    41 # with '#' will be ignored, and an empty message aborts the commit.
    42 # Not currently on any branch.
    43 # You are currently editing a commit during a rebase.
    44 #
    45 # Changes to be committed:
    46 #   (use "git reset HEAD^1 <file>..." to unstage)
    47 #
    48 #       modified:    NerfGunChart.txt
    49 #

Since this is ultimately a commit message, it is treated in a typical Git manner.  Modify it all you want to make it more concise.  If you're not happy with the squash and want to abort the entire process, just make the commit message empty.  However, if this step is taken, you get booted into a `((no-branch))` detached-head mode and will have to delete an existing `.git/rebase-merge` folder.  Simply checkout the branch you want instead and then follow the simple instructions that git ships with when another action may have to occur while in the middle of a git process.  These typically explain exactly what steps are needed to fix your current git state.  Follow accordingly.  

*Scenario*: I'm the only one that's going to be working on this repository.  It makes me look better when I have a very concise history.  Let me just keep all of my changes in my non-staged area and commit everything all at once.  _Worky, worky, worky._ Ok, now let me me commit everything.  Ah fuck!  I accidentally did a `checkout` instead of a commit on all my files.  Now all my changes are lost...

*Solution*: Commit often.  Give a detailed, but as-terse-as-possible (yes, oxymoron, but can be done, in my opinion) commit message so it is more apparent to everyone what actions were taken rather than needing to dig through the code to figure out the logic.  Sometimes a very short commit message is unavoidable like when you're literally updating just whitespace characters.  But if it is something that requires more explaination, please do explain.  

*Scenario*: I have to type too much when I push and pull.  My fingers hurt.  

*Solution*: Tired of doing this:

    (feature-branch-name-is-long) $ git pull origin feature-branch-name-is-long
    ... changes and commits ...
    (feature-branch-name-is-long) $ git push origin feature-branch-name-is-long

Set your upstream branches on the remote.  There are a couple easy ways to do this.  The easiest is when you're creating your new branch.  Rather than doing this:

    (feature-branch-name-is-long) $ git push origin feature-branch-name-is-long

Simply add the `-u` flag:

    (feature-branch-name-is-long) $ git push -u origin feature-branch-name-is-long

Oh, you didn't do that from the get-go?  You're not out of luck then!  Run this command to an existing branch with no upstream set:

    (feature-branch-name-is-long) $ git branch --set-upstream origin feature-branch-name-is-long

Regardless of how the upstream was set, your fingers can get lazier and as long as you know you're on the right branch, you can make your workflow more effiecient:

    (feature-branch-name-is-long) $ git pull
    ... changes and commits ...
    (feature-branch-name-is-long) $ git push

A little extra - for even lazier fingers, alias these commands in your dot-files:

    alias gpl='git pull'
    alias gpu='git push'











