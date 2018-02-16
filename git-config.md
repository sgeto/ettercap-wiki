## Guideline for (new) contributors
This page outlines a good guideline for new contributors who are not used to *git* and *Github*.
A few things are due before start contributing code to the project. This doesn't claim to be a git man page, but just an easy guide to start coding for ettercap in 10 minutes. If you are new to Github at all, lets start with some explanation beforehand.

The philosophy behind Github is slightly different to the traditional *BugZilla* and *Mailinglist* approach. 
Patches are not submitted using Bugzilla, they are submitted by creating a pull-request *(pull code from other forks back into the original master)*. Each pull-request is a set of commits which changes are discussed over the Github web front-end before they gonna be merged. However, email can also be used to get updates of the discussions and even to answer.

Before we proceed I want to mention that there is even a way to create a pull-request without getting bothered by anything around git and what's described below. For small changes you can also just edit the file using the Github inline editor and save your changes. This automatically creates a pull-request without forking the repository or knowing git!

But if you plan to hand in more complex changes, we still recommend to read further and discover the power of git.

### A useful set of Github tricks!
First of all, be adviced about [this](https://github.com/tiimgreen/github-cheat-sheet) impressive collection of tips and tricks to use Github in a more efficient way.
If you want to use graphic clients you can install **gitg** *(Gnome/GTK)* or **gitk** *(KDE/QT)*. However this guide focuses on the CLI command **git**.

### Basic steps before start helping:
* setup a github account
* fork ettercap project
* clone your fork locally (`$ git clone https://github.com/<youraccountname>/ettercap`)

Now you have the source code of ettercap available on your computer. But before you do your changes, please proceed reading that helps in maintaining your repository.

### Guidelines how to contribute to the project
#### Link your local copy with the ettercap master repository
The next step is to link the ettercap master repository as the upstream of your local copy:

    $ git remote add upstream https://github.com/Ettercap/ettercap.git

this way you get the latest updates on the *upstream* repository by calling

    $ git fetch upstream

To automatically fetch the latest pull-requests you have to edit your `.git/config` *(relative to your local copy)* and add in the upstream section the following line

    fetch = +refs/pull/*/head:refs/remotes/upstream/pr/*

Next time you fetch the upstream using the above fetch-command you will also fetch the pull requests which are treated as remote branches. To test a particular pull-request, create and change into a new branch from your master branch and merge the changes of the pull-request by using the command:

    $ git checkout -b test-PullRequest
    $ git merge upstream/pr/Number


#### Always work with branches
Keep your master branch clean! Use it just as a copy of the latest master branch of ettercap. You can keep it up 2 date using:

    $ git fetch upstream 
    $ git checkout master
    $ git merge upstream/master
    $ git push origin master

Leaving your master "clean" will avoid merge issues when fetching new commits.

When you work on a particular fix/issue/feature, it is good practice and strongly recommended to create a dedicated branch for it, this will help a lot in reviewing the code.
DON'T fix two or more issues in a single branch! Better do three different branches and pull requests instead, unless commits are strictly correlated.

    $ git checkout master
    $ git checkout -b fix-Number

Now you can do your changes. All these changes are related to this branch (check `$ git branch` to find out on which branch you're currently working on). After you finished your work, sync your local branch to your remote repository on Github:

    $ git push origin fix-Number

Note that fix-Number is just an example for a branch name and can be replaced by anything else.

*if you want to create a new branch for fixing another issue it is always better switch back to your master branch and to start from there*


#### Commit your changes in your local branch
If you've done some changes you need to save them to the history of your branch as commits. If your changes affect only one file or multiple that belong all together to one "change", you can commit them all in one row.

First check the status of your branch, if it's really in the state you expected. If everything is fine you can commit all changed files in one commit:

    $ git status
    (check the output of the status command)
    $ git commit -am "commit message for the history"

*If possible use a one-line message that quickly abstracts the change of the commit.*

If you want to split the changes (changed files) into multiple commits, first you have to add the files desired for the next commit and then do the commit. After that you can add the next set of files and so on...

Splitting changes into multiple commits is desired to provide transparancy if the changes are quite complex. If everyone understood your changes, you can still rebase some commits together (melting) to keep the history of ettercap master clean.

    $ git add <file1>
    $ git add <file2>
    $ git commit -m "this is the first part of the change"
    $ git add <file3>
    $ git commit -m "this is the second part of the change"


#### Inspect the history of a branch
To see what has been changed in a specific branch, use the log command.

    $ git log

*Note that the use of `git whatchanged` is depricated and should not be used for new users. It's kept for historical reasons only and is just a equivalent of `git log` just with different defaults.*


#### Keep in sync with changes on the upstream repository
Sometimes it happens, that your or someone elses changes have been merged into the ettercap master. In such cases conflicts might occur that prevent your branch (pull-request) to be merged into the ettercap master.
To resolve this you should rebase your changes onto the latest changes of upstream. First fetch the latest upstream changes then just rebase your commits (rewind your changes, merging upstream/master and commit again).

    $ git fetch upstream
    $ git rebase upstream/master

In some cases you are prompted to resolve conflicts by hand.

    $ vi <conflictingfile>
    $ git add <conflictingfile>
    $ git rebase --continue

**NOTE:** When you rebase, your commit hashes change. You will fail to push again to your repository. You have to force the push (overwrite the history on the remote branch).

    $ git push -f origin <yourbranch>


#### Revert the history of a branch
In some cases it may become useful to turn back the time and move a branch back to a state in the past. It is very useful if someone else rebases a branch you're also working on. If you would in that case just pull the updates, it would clutter your history with merge entries that are not really necessary. Or if you work on the same branch from different computers (local copies of your repository). In such cases it is better to revert the history of your version of the branch to some state before, and then pull the remote changes again.

But use this command with caution as it will discard all the commits you've done in between. If there have been unpushed commits you have to save them otherwise they are lost.

Display the history log upfront to find the appropriate commit id desired to reset to:

    $ git log --oneline 
    (copy the last commit id you want to keep)
    $ git reset --hard commit-id

*You still can recover your history even after a reset "hard", but this isn't the topic of this guide, git reflog helps you anyway*


#### Export/saving commits as patches and importing commit patches
In several cases it is very usefule to export some commits into patch files. Don't be surpised about the format of the file as meta information is stored in the typical email format. It is for example very useful of you want to reset your branch but not loose your latest commits. You export them prior, revert your history and maybe pull again a changed/updated history and reimport your patches.

To export the last n commits use the command:

    $ git format-patch -n

This will create n patch files in your current working directory. To import such patch files use the following command:

    $ git am <patch-filename>

It will automatically keep the author, the commit message, commit id and every git reference.

#### Manipulating the history
In some cases, it is very useful to influence the history of a branch. For example if you documented a quite complex change in a number of separate commits to make the complex change transparent, you might not want these chatty commits apprear in the ettercap master history when your pull-request is merged. You might want to melt the related commits together. To achieve that, the interactive *rebase* command is a very useful tool.

You can either rebase the n last commits or rebase starting at a specific commit id:

    $ git rebase -i HEAD~2
    (this rebases the last 2 commits)
    $ git rebase -i deadbee
    (this rebases the history starting at commit-id "deadbee")

After that your favorite editor openes up and shows you the list of affected commits (*note the reverse order*) with the word "pick". You can 
* move the lines if you want to reorder the commits;
* delete a line to drop a commit;
* replace "pick" with the word "fixup" or just "f" to melt the commit with the above one (multiple times allowed);
* and some more.... (don't want to replace the man-page)

Once you save these changes, rebasing starts and a the editor may show up again asking for potential changes in the commit messages that changed. Once you save and quit this step your history has been rewritten. Be adviced, that it's not possible to move around commits or to fixup commits which conflicts each other. In such cases you can type `git rebase --abort` to abort the process.

**NOTE:** your commit ids have been changed for the commits in scope of the rebase. If you want to push this changed history back to your repository, you need to use forced push.

### After the pull request has been merged
After your pull request has been accepted and merged you can safely remove your branch. Switch to the master branch and delete your branch. You can as well delete your remote branch on your Github repository using the CLI.

    $ git checkout master
    $ git branch -D branch-name
    $ git push origin --delete branch-name

You can as well delete your remote branch using the web front-end on Github which is automatically suggested to you as soon as your pull request has been merged.