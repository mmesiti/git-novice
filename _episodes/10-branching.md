---
title: Branching
teaching: 25
exercises:  15 
questions:
- "How can I keep track of different lines of work efficiently?"
objectives:
- "Be able to create and merge branches."
- "Know the difference between a branch and a tag."
keypoints:
- "use `git branch` to view, create and delete branches."
- "use `git tag` to view, create and delete tags."
---

So far, every time 
we have made a change to our repository 
and commited it, 
we always built 
on top of the work 
we had done previously.
When collaborating with other people, though,
we have worked independently, 
starting from a common commit,
and then we have *merged*
our contributions together
by *pulling* from a remote repository
into a local repository.
 
We can look at the graph of commits in our repository
with the command

~~~
git log  --oneline --decorate --graph --all
~~~
{: .language-bash}
~~~
*   a53b0af (HEAD -> main, origin/main, origin/HEAD) Merge changes from GitHub
|\  
| * 3eb9f7c Add a line in our home copy
* | 5f992dd Add a line in my copy
|/  
* 2d8a740 Add notes about Pluto
* 54765f6 Ignore data files and the result folder.
* 47122ce Add some initial thoughts on spaceships
* 66ca75a Discuss concerns about Mars' climate for Mummy
* 0aeaae3 Add concerns about effects of Mars' moons on Wolfman
* 118561d Start notes on Mars as a base
~~~
{: .output}

We notice that the commits 
do not sit on a single line, 
but there is a branching point and a merge.

> ## Saving keystrokes and avoiding mistakes: git aliases
> The command above
> ~~~
> git log  --oneline --decorate --graph --all
> ~~~
> {: .language-bash}
> tends to be very useful, but it takes very long to type,
> and making a mistake while typing it is very likely.
> For this reason, git allows *aliases* to be configured, 
> with, for example
> ~~~
> git config --global alias.graph "log --oneline --decorate --graph --all"
> ~~~
> {: .language-bash}
> After configuring the alias in this way,
> we can use our custom-defined command `git graph` 
> in place of the long one, with the same effect.
{: .callout}

All this time, though,
we have never explicitly created any branch
nor explicitly merged one,
and have kept working 
on the *main* branch.

This might be perfectly fine
for simple development workflows,
but for software development tasks
we might benefit from a more sophisticated approach.

In particular:
- We need to keep reference to a version (commit)
  of the code that we trust 
  (e.g., that works as expected, give expected results)
- We want to work on different features, 
  and the develoment of each feature 
  might take several commits
- We want to be able to make experimental changes
  in our code without breaking the working version
  or the work on other features

Git facilitates these kind of workflows 
with a powerful branching mechanism,
that allows the use to **isolate different tracks of work**
which can later be merged together.
Each repository can have a number of branches,
which can be matched to branches in other repositories
(when pushing and pulling).


To view and manage branches
we can use the `git branch` command:
~~~
$ git branch
~~~
{: .language-bash}

~~~
* main 
~~~
{: .output}

By default, it prints out a list of branches 
available in the repository we are in,
marking with a star the branch we are working on 
at the moment.
  
> ## What is a branch? 
> When we look at a commit graph, 
> we identify a branch as 
> (from the [Oxford Learner's Dictionaries][oxford-dict]):
>
> > A part of a tree that grows out from the main stem [...].  
>
> We might also improperly refer to a branch 
> as *a group of commits that creates a single narrative*.
> But in the context of Git, a branch is just a human-readable name
> that points to a commit - keeping to the botanic analogy, 
> a leaf usually at the tip of the branch. 
> If we make more commits on a branch,
> the pointed commit will be updated.
>
> In particular, this means that deleting a branch
> does not mean deleting any work
> (although, commits that cannot be reached from any branch
> might be automatically deleted at some point).
{: .callout}

## Creating branches and working with them

At some point, Dracula has the idea 
to get a gravity assist from Venus 
to get to Mars faster. 
It is not at all clear if this would work or not,
so he decides to save all the work based on this idea 
in another branch.

Using the `git branch` command with some arguments
~~~
git branch gravity-assist main
~~~
{: .language-bash}
we create another branch called `gravity-assist` 
that will "graft" onto the last commit on the `main` branch.
We can verify that we created a new branch
with the `git branch` command again, without arguments this time:
~~~
git branch
~~~
{: .language-bash}
~~~
  gravity-assist
* main
~~~
{: .output}
We notice that we are still on the `main` branch. 
To switch to the new branch,
we use `git checkout`:
~~~
git checkout gravity-assist
~~~
{: .language-bash}
~~~
Switched to branch 'gravity-assist'
~~~
{: .output}
Optionally, we can verify again the branch we are on with `git branch`.
Whatever work we do now is not going to affect the `main` branch,
and we are free to experiment as much as we like. 
We add a file called `venus.txt` with the following content:
~~~
The orbital period of Venus is 224 days.
~~~
{: .output}
We can now add our new file to the staging area and commit:
~~~
git add venus.txt
git commit -m "Start notes about Venus"
~~~
{: .language-bash}

It turns out we did not properly round the orbital period of Venus, 
which is actually 224.7 days.
Let us correct that, and make another commit
~~~
The orbital period of Venus is 225 days.
~~~
{: .output}
~~~
git add venus.txt
git commit -m "Fix orbital period of Venus"
~~~
{: .language-bash}

We want to publish the branch we are working on 
to GitHub, so we can discuss the work 
and share it with collaborators 
(and also feel safer in case anything bad happens 
to our laptop):
~~~
git push origin gravity-assist
~~~
{: .language-bash}
We can check on GitHub that a new branch was created.
While we wait for feedback,
we decide to continue our work on the main branch:
~~~
git checkout main
~~~
{: .language-bash}
We make sure that we are on the `main` branch again with `git branch`:
~~~
git branch
~~~
{: .language-bash}
~~~
  gravity-assist
* main
~~~
{: .output}

We keep working on `mars.txt`,
adding the following line:
~~~
Landing on Mars (successfully) has historically proven difficult.
~~~
{: .output}
Then we add the changes and commit them:
~~~
git add mars.txt
git commit -m "Add notes on difficulty of the endeavour"
~~~
{: .language-bash}
We can check our commit graph again with the `git log` command 
(using the arrow up key to get it from the bash history):
~~~
git log  --oneline --decorate --graph --all
~~~
{: .language-bash}
~~~
* 0746194 (HEAD -> main) Add notes on difficulty of the endeavour
| * 8da5718 (gravity-assist) Fix orbital period of Venus
| * ed6944a Start notes about Venus
|/  
*   a53b0af (tag: 09-conflict-resolution, origin/main, origin/HEAD) Merge changes from GitHub
|\  
| * 3eb9f7c (tag: 09-conflict-principal) Add a line in our home copy
* | 5f992dd (tag: 09-conflict-collaborator) Add a line in my copy
|/  
* 2d8a740 (tag: 08-collab) Add notes about Pluto
* 54765f6 (tag: 06-ignore) Ignore data files and the result folder.
* 47122ce (tag: exercises-04-changes) Add some initial thoughts on spaceships
* 66ca75a (tag: 04-changes) Discuss concerns about Mars' climate for Mummy
* 0aeaae3 Add concerns about effects of Mars' moons on Wolfman
* 118561d Start notes on Mars as a base
~~~
{: .output}

## Merging branches

After discussing with our collaborators, 
we decide that the gravity assist is worth pursuing,
and we want to merge the work on the `gravity-assist` branch into `main`.
We make sure once again that we are on the `main` branch:
~~~
git branch
~~~
{: .language-bash}
~~~
  gravity-assist
* main
~~~
{: .output}
We are then ready to merge `gravity-assist` into `main`:
~~~
git merge gravity-assist
~~~
{: .language-bash}
This will open a text editor for a commit message.
We can leave the default message as it is,
save and exit the editor.
We can check again the commit graph:
~~~
git log  --oneline --decorate --graph --all
~~~
{: .language-bash}
~~~
*   691f9d2 (HEAD -> main) Merge branch 'gravity-assist'
|\  
| * 8da5718 (gravity-assist) Fix orbital period of Venus
| * ed6944a Start notes about Venus
* | 0746194 Add notes on difficulty of the endeavour
|/  
*   a53b0af (tag: 09-conflict-resolution, origin/main, origin/HEAD) Merge changes from GitHub
|\  
| * 3eb9f7c (tag: 09-conflict-principal) Add a line in our home copy
* | 5f992dd (tag: 09-conflict-collaborator) Add a line in my copy
|/  
* 2d8a740 (tag: 08-collab) Add notes about Pluto
* 54765f6 (tag: 06-ignore) Ignore data files and the result folder.
* 47122ce (tag: exercises-04-changes) Add some initial thoughts on spaceships
* 66ca75a (tag: 04-changes) Discuss concerns about Mars' climate for Mummy
* 0aeaae3 Add concerns about effects of Mars' moons on Wolfman
* 118561d Start notes on Mars as a base
~~~
{: .output}

We can then push our changes to main to the remote repository:
~~~
git push origin main
~~~
{: .language-bash}

## Deleting branches safely

After a branch has been merged
it is good practice to delete it.
We can delete `gravity-assist` with the `git branch -d` command:
~~~
git branch -d gravity-assist 
~~~
{: .language-bash}
~~~
Deleted branch gravity-assist (was 8da5718).
~~~
{: .output}
We can check the commit graph again:
~~~
git log  --oneline --decorate --graph --all
~~~
{: .language-bash}
~~~
*   691f9d2 (HEAD -> main) Merge branch 'gravity-assist'
|\  
| * 8da5718 Fix orbital period of Venus
| * ed6944a Start notes about Venus
* | 0746194 Add notes on difficulty of the endeavour
|/  
*   a53b0af (tag: 09-conflict-resolution, origin/main, origin/HEAD) Merge changes from GitHub
|\  
| * 3eb9f7c (tag: 09-conflict-principal) Add a line in our home copy
* | 5f992dd (tag: 09-conflict-collaborator) Add a line in my copy
|/  
* 2d8a740 (tag: 08-collab) Add notes about Pluto
* 54765f6 (tag: 06-ignore) Ignore data files and the result folder.
* 47122ce (tag: exercises-04-changes) Add some initial thoughts on spaceships
* 66ca75a (tag: 04-changes) Discuss concerns about Mars' climate for Mummy
* 0aeaae3 Add concerns about effects of Mars' moons on Wolfman
* 118561d Start notes on Mars as a base
~~~
{: .output}
We see that the only the "pointer" 
to the `gravity-assist` branch is gone,
but not the commits,
which have been already safely merged into `main`.

Git will not let you delete a branch 
which has not been merged in an other existing branch,
unless you insist by using `git branch -D`.
Even then, the commits are not lost,
but they might be very hard to find 
as there is no branch pointing to them.

## Tags

Tags are similar to branches,
in the sense that they are a "pointer" to a commit,
but contrarily to branches they do not change over time.
They are a mechanism to elect some commits
to a more important status.



> ## Other commands to create branches
> Creating a branch can be also done 
> with `git checkout` and with `git switch`,
> using the right options. 
> Can you find the right options from the man page?
> 
> Hint: use `git help checkout`
> and `git help switch`, 
> and type `/create` to search for the word "create".
> > ## Solution
> > The command 
> > ~~~
> > git checkout -b new-branch
> > ~~~
> > {: .language-bash}
> > does create a new branch and checks it out,
> > it is a shortcut for `git branch new-branch ; git checkout new-branch`.
> > The command 
> > ~~~
> > git switch -c new-branch
> > ~~~
> > {: .language-bash}
> > does the same. 
> > The `git switch` command is possibly easier to remember,
> > but it might not be available in older versions of Git.
> {: .solution}
{: .challenge}

> ## Delete remote branches 
> Using the `planets` repository as a test playground,
> create a branch called `test-branch`,
> and push it to `origin`.
> Verify that the branch is there.
> To delete it, use
> ~~~
> git push origin -d test-branch
>  ~~~
> Verify that the branch is not there anymore.
{: .challenge}

> ## Using Tags
> Using the `planets` repository as a test playground,
> create a tag using the command
> ~~~
> git tag -a v0.1 -m "A test tag" 
> ~~~
> {: .language-bash}
> You can verify that a new tag was created wit the 
> `git tag` command (without arguments).
> Check on the web page of your remote repository on GitHub 
> (or wherever `origin` is): are any tags listed?
> (The number of tags is usually shown at the top of the page)
> 
> Push the tag to the remote repository with 
> ~~~
> git push origin v0.1
> ~~~
> {: .language-bash}
> Can you see it on the remote repository now? 
>
> ## Deleting Local Tags
> Deleting branches is done with `git branch -d`.
> Can you figure out how to delete a tag? 
> > ## Solution
> > To delete a tag locally:
> > ~~~ 
> > git tag -d <tag-name> 
> > ~~~ 
> > {: .language-bash}
> {: .solution}
>
> ## Deleting Remote Tags
> Create a tag called `v0.2` with message "test tag please ignore",
> and push it to the remote repository.  
> To delete a branch named `test-branch` 
> on the remote repository called `origin`
> One has to do `git push origin -d test-branch`.  
> Can you figure out how to delete a tag on a remote repository?
> > ## Solution 
> > The web interface 
> > on GitHub or GitLab
> > provide the options to
> > to delete a tag 
> > in the menus.
> > Alternatively, one can use exactly the same command as for branches:
> > ~~~
> > git push origin -d test-tag
> {: .solution}
{: .challenge}


> ## Hard: recover deleted branches
> 1. Create a new branch, add some work to it,
>    and commit it. Do not merge it with any other branch.
> 2. Force-delete it with `git branch -D`.
> 3. How can you retrieve the lost work, by looking at the terminal?
> 4. Can the `git reflog` command be useful?
> 
> > ## Solution
> > After you saved the last commit message.
> > you should have got a commit hash in the output message. 
> > You can re-create a branch 
> > named `rescue-branch`
> > pointing to that commit with
> > ~~~
> > git branch rescue-branch-name <commit-id-recovered-from-terminal>
> > ~~~
> > {: .language-bash}
> > A more robust way to recover commit hashes 
> > is the command `git reflog`, 
> > which shows the history of the movements of HEAD.
> > Nothing is really lost when using Git. Even if you delete branches!
> {: .solution}
{: .challenge}
## Copyright notice


This episode is based on/inspired by the [episode on branching][coderefinery-branching]
in the course [Introduction to Version Control with Git][coderefinery-gitintro] developed by [CodeRefinery][coderefinery]

[oxford-dict]: https://www.oxfordlearnersdictionaries.com/
[coderefinery-branching]: https://coderefinery.github.io/git-intro/branches/
[coderefinery-gitintro]: https://coderefinery.github.io/git-intro/
[coderefinery]: https://coderefinery.org/






