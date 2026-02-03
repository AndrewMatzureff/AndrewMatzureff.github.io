---
layout: post
title:  "Push Changes To GitHub"
date:   2026-01-29 20:00:00 -0500
categories: newforce sop
---

1. First off, be sure to save any work you've done in whichever application you're working from!
<br>	![step-1-save_work.png](/assets/newforce/sop/push_changes_to_github/step-1-save_work.png)
    <pre><b>⚠ NOTE: <i>If you have your work saved to a location outside of your local git repo you will need to copy or move the saved file(s) into your local repo!</i></b></pre>

2. Using GitBash or your terminal of choice navigate to the folder containing your local git repo and then `cd` into it.
<br>	![step-2-cd_repo.png](/assets/newforce/sop/push_changes_to_github/step-2-cd_repo.png)

3. It's always helpful to perform a cursory `git status` and/or `git log` to remind yourself of the current state of your working tree. If you don't see your changes under "modified files" (if you've edited an existing file) or "untracked files" (if you've created a new file since your last commit) or you are not checked out to the branch you intended then this is your opportunity to investigate why the contents of your repo are not as you expect. If everything checks out (no pun intended) then proceed with confidence!
<br>	![step-3-git_status.png](/assets/newforce/sop/push_changes_to_github/step-3-git_status.png)
    <div style="border: 1px solid yellow; padding: 10px;">
    <pre><b>⚠ NOTE: <i>You can get a detailed printout of your working tree's commit history including commit messages and branches using `git log`. <br>To get a more concise, formatted, visual representation of your commit history you can use an alias such as `git lol` to output the log in a specific, desired format: <br></i></b>```<br># Assign a concise, pretty-formatted commit log command to a short, convenient alias ("git lol"). This only needs to be run once to be able to use "git lol" whenever and wherever.<br>git config --global alias.lol "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"<br>```</pre>
    </div>

4. Once you have confirmed that your changes are ready and your local git environment accurately reflects your expectations, go ahead and stage your new/modified files using `git add` making sure to continue validating your expectations using `git status` and/or `git log` as you go.
<br>	![step-4-git_add.png](/assets/newforce/sop/push_changes_to_github/step-4-git_add.png)

5. After you've staged all the files you'd like to include in your commit, you may save their current state to the tip of your current local branch while providing a helpful message to describe the changes by using: `git commit -m "Your message here."`.
<br>	![step-5-git_commit.png](/assets/newforce/sop/push_changes_to_github/step-5-git_commit.png)
    <div style="border: 1px solid yellow; padding: 10px;">
    <pre><b>⚠ NOTE: <i>Make sure that you are checked out to the very tip (latest commit) of your current branch! <br>You can verify this by using `git lol` (see note at step 3 for instructions on how to set up `git lol`) to check for `(HEAD -> your-branch-name)` where `your-branch-name` refers to the branch you want to make changes to. <br>After committing your changes another `git lol` should show 1 new line above the previous latest commit (e.g: the current state of your remote repo at `origin/main` and `origin/HEAD`) containing your new commit alongside your message at the tip of your branch. <br></i></b>```<br># Print out a concise, pretty-formatted commit log so that you can visually verify the state of your working tree.<br>git lol<br>```</pre>
    </div>

6. If everything looks good locally you are free to `git push` your latest changes to GitHub!
<br>	![step-6-git_push.png](/assets/newforce/sop/push_changes_to_github/step-6-git_push.png)
    <div style="border: 1px solid yellow; padding: 10px;">
    <pre><b>⚠ NOTE: <i>Make sure that your remote branch is now synced to your local branch! <br>You can verify this by using `git lol` (see note at step 3 for instructions on how to set up `git lol`) to check for `(HEAD -> your-branch-name)` where `your-branch-name` refers to the branch whose latest changes you just pushed. <br>After pushing your changes another `git lol` should show that the current state of your remote repo (e.g.: at `origin/main` and `origin/HEAD`) now corresponds to the latest commit you just created at the tip of your local branch. <br></i></b>```<br># Print out a concise, pretty-formatted commit log so that you can visually verify the state of your working tree.<br>git lol<br>```</pre>
    </div>

<div style="border: 1px solid white; padding: 10px;"><h3>Looking for another SOP?</h3></div>
- Visit: [NewForce SOP Index]({{ site.baseurl }}{% link _posts/2026-02-03-NewForce_SOP-index.markdown %})