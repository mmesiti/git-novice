---
title: Branching
teaching: 35
exercises:  15 
questions:
- "FIXME"
objectives:
- "Be able to create and merge branches."
- "Know the difference between a branch and a tag."
keypoints:
- "use `git branch` to view, create and delete branches."
- "FIXME"
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
but there are branching points and merges.

> ## Saving keystrokes: git aliases
> The command above
> ~~~
> git log  --oneline --decorate --graph --all
> ~~~
> {: .language-bash}
> tends to be very useful, but it takes very long to type,
> and it is quite likely that you will make a mistake while typing it.
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
> as a group of commits that creates a single narrative.
> But in Git terms, a branch is just a human-readable name
> that points to a commit - keeping to the botanic analogy, 
> a leaf at the tip of the branch. 
> In particular, this means that deleting a branch
> does not mean deleting any work
> (although, commits that cannot be reached from any branch
> might be automatically deleted at some point).
{: .callout}



[oxford-dict]: https://www.oxfordlearnersdictionaries.com/




