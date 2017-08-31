# Git Rebase Tutorial
A helpful tutorial for those wanting to learn how to rebase using Git.

I'm going to be adding a new line.  This will be my new first new commit. I'll be using Git Commit Template... hopefully.

Cool.  So, the Git template works.  Anything commented out won't appear.


# Trunk Based Development

Source: [https://trunkbaseddevelopment.com/](https://trunkbaseddevelopment.com/)

## Introduction

This writeup allows for an individual to get started with Trunk Based Development, and how our team with implement it.  For a full understanding, refer to the link above.

This guide will cover our branching and merging strategy.

>A source-control branching model, where developers collaborate on code in a single branch called ‘trunk’, resist any pressure to create other long-lived development branches by employing documented techniques, avoid merge hell, do not break the build, and live happily ever after.

Trunk-based development allows for Continuous Integration and Continuous Delivery (CI/CD) because team members commit their changes to master (the trunk) multiple times a day.  This is the main requirement for Continuous Integration.  This ensures that the codebase is always releasable on demand and helps with Continuous Delivery.

### Claim

* You can either do a direct to trunk commit/push (small teams) or a Pull-Request workflow as long as those feature branches are short-lived and the product of a single person.

### Caveats

* **Short-lived branches** are key.  These branches are mainly for code review and build checking (CI).  This allows for quality control before changes get merged back into the trunk.  This allows for continuous code reviews.
* **Feature flags** comes in handy with day-to-day development.  Allows for hedging on order of releases.
* **Build server** can verify that commits don't break the build after they land in the Trunk and after they're ready to be merged back into the Trunk. CI tools like Jenkins, TravisCI, and GitLab CI can all be used.
* **GitHub-flow** is very similar to Trunk based development. There's only one small difference. 
* **Gitflow** is **very different**

More information regarding GitHub-flow vs Gitflow can be found [here](https://lucamezzalira.com/2014/03/10/git-flow-vs-github-flow/).

### Name

The word "trunk" in trunk-based development refers to the concept of a growing tree.  The fattest and longest part of a tree is the trunk, not the branching radiating from it.

## Feature Flags

Feature flags is a way to control the capabilities of an application or service in a large decisive way. Depending on the feature, the team might determine that the code is allowed to be merged into the Trunk, but only turn on the feature after certain features are truly merged back into the Trunk.

> Flags are toggles. Martin Fowler calls this technique 'Feature Toggles'.

### Granularity

The flag controls something large, like the UI of a component. Our flag could be ```OneClickPurchasing``` or ```ShoppingCart```.

### Implementation

Flags can be achieved through arguments. Then, implemented through Java.

For example:

```
if args.contains ("--withOneClickPurchase") {
	purchasingCOmpleting = new OneClickPurchasing();
}
```
Then, through Config:

```
bootContainer.addComponent(classFromName(config.get("purchasingCompleting")));
```

### Continous Integration Pipelines

It's important to have CI house your expected flags. This ensures that your tests can adaptable and is meaningful for each of your flag permutations.

### Runtime Switchable

Flags that are set at launch time isn't enough. Key for runtime switchable flags is the need for the state to persist. A restart of the application or service should not set that flag choice back to default.

### Build Flags

These flags affect the application or service as it is being built.

### A/B Testing and Betas

Pushing code that's turned off into production allows you to turn it on for ephemeral reasons.  You want a subset of your users to knowingly or unknowningly try it out.

### Technical Debt

Flags put into your codebase over time can get forgotten.  You want to make sure that the feature is fixed in the toggle state. Once ready, feature toggles should be removed once code is ready to go live.

For more information, refer to [here](http://featureflags.io/).

## Release from trunk

Teams with a very high cadence do not need, and cannot use, release branches.  They have to release from the trunk.

Most teams do not use a numerical release scheme.  Instead, they use a format that references the commit number or date and time.  Also, when a bug arises, it's fixed as a feature, as quickly as possible.

> Branches can be made retroactively. You can choose the revision in the past to branch from.  The outcome is exactly the same as if you had made it at the time.

## Short-Lived Feature Branches

Either you branch off of master, or you fork an entire repository.  These branches are going to come back as a "pull/merge request".  

A rule of thumb is the length of life of the branch before it gets merged and deleted. The branch should be short lived and only last a couple of days.  For our team, our sprints last two weeks, so they should only last, at most, ten days. In reality, these branches should only be living around 2 to 3 days, if possible.

Also, another rule of thumb is the amount of developers that are allowed to live on a specific branch. The answer: one.  If you are pair-programming, two. These short-lived branches aren't shared within the team for general development.  They only exist for code reviews. If you find yourself needing code from another branch, the task didn't get broken down far enough.

### Merge Directionality

Short-lived feature branches are real branches and merge is a first class concept.  In completing work on short-lived branches, you will need to bring it up to date with master (trunk). 

Recap: Merges to the short-lived feature branch are allowed to bring it closer to HEAD of master (trunk).  Merges to master (trunk) are allowed only as part of closing out the short-lived feature branch (and just before deleting it).

### Misc

At some point, these short-lived branches have to be brought back up to date with master (trunk).  

#### Git Stash

Some people do ```git stash``` before ```git pull```, and before ```git stash pop```.  There's a chance that when you pop your working copy may be in a merge clash situation that has to be resolved before you progress.  This way will always result in your change being a single commit, at the HEAD of the branch.

#### Git Rebase

Some people do ```git rebase```. Even with this model, you might encounter a merge conflict and have to resolve it.

## Branching Strategy

For our branching strategy, our team is using trunk-based development. This means that, we will only have one main branch, master (trunk). Any features that we have will branch off of master.  These feature branches will be short-lived. A merge request is created, and this will be used to code review. Once branch is approved, the commits can be rebased and squashed.

Once these feature branches are ready to be merged in, the merge request is accepted, and the feature now becomes part of the trunk.  Then, the feature branch is deleted.

## Merging Strategy

Commit Early, Commit Often, Perfect Later, Publish Once

### Step 1: Do Not Push Often

Pushes should be reserved for when chunks of logic is complete. 

You should:

* Commit early
* Commit often
* No need to compile
* No need for CI
* It's only for versioning

You should commit often because you can't revert back to an earlier point in time if they don't exist.  Git is a "time machine".  You need reference points to go to an earlier time in the codebase.

### Step 2: Pull with Rebase

This allows you to:

* Get forced pushes securely
* Rebase your commits

```git pull --rebase origin master```

If your branch is already pushed:

```git push -f```

Then, sync your source branch:

```git fetch origin master:master```

### Step 3: Perfect later & Make it a Single Commit

At this point:

* Tests are passing
* App is working
* Code is reviewed

You have a couple of ways you can do this: ```git reset HEAD~3``` or ```git rebase -i HEAD~3```

You can either reset HEAD or interactive rebase HEAD.  When you reset HEAD, you reset HEAD back to N commits earlier.  Then, you would commit under a new message.

### Step 4: Feature Flags/Toggles

Use feature flags/toggles if the feature should be disabled.  Then, merge back to source.

```git checkout master``` & ```git merge branch```

### Summary

You should:

* **Commit early & often** - Perfect later, publish once philosophy
* **Trunk Based development** - Used to govern overall
* **Delivery frequently** - Be prepared to send every single commit
* **Deleting branches after merge** - Will make commit graph readable
* **Continuous Integration** - Validates master branch continuously
* **Pull/Merge Requests** - Can be used to review code and to validate before merging back to master
* **Feature Flags** - Should be used whenever possible

### Do Not Lose Uncommited Code

#### Stashing

Use stashing when you have uncommitted changes, but you need to switch branches.

```git stash save "Updated README"```
```git reset --hard HEAD~```
```git stash apply stash@{0}```

#### Hard Reset

You can perform a hard reset with merge.

```git reset --merge HEAD~```

#### Create A New Branch (and commit into it)

```git checkout -b feature/CP-123```
```git add README.md```
```git commit -m "Add more info"```

### Commit Messages

Commit message can be really helpful when debugging or looking at changes months from now.  For our team, we are following this [commit guide](https://chris.beams.io/posts/git-commit/). These are the main points:

1. Separate subject from body with a blank line
1. Limit the subject line to 50 characters
1. Capitalize the subject line
1. Do not end the subject line with a period
1. Use the imperative mood in the subject line
1. Wrap the body at 72 characters
1. Use the body to explain what and why vs. how
1. When using lists, use dashes

You can use Git Commit Templates to create better commit messages.

Once the file is created:

```git config --global commit.template <path to file>/git-commit-template.txt```

```git config --global commit.cleanup strip```

Then, you can use the command ```git commit```. This will bring up an editor that has the commit template.  Then, you fill in the relevant information you want.

**NOTE**: The commit template contains lines with '#'.  These lines are ommited when actually committed.  For more information, refer to [here](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration) under the ```commit.template``` section.

### Rebasing

Rebasing is the process of rewriting the project commit history. This can help to make our commit history cleaner by eliminating all of the unnecessary commits.

For example, if you are working on a feature that adds a new view to a website. You might want to make commits when you have certain aspects completed, styles are correct, debugging some logic, adding documentation, and finshing some tests. These commits help the developer during development, and allows for their code to be constantly code reviewed.  However, if these commits were pushed to master, the commit history would become extremely dirty because it would be difficult to discern what features are what.  By rebasing, you can squash, or convert every commit into one, all the commits and merge that into master.

#### Trade-offs

There are two main trade-offs with rebasing:

1. Safety
1. Traceability

For safety, a developer needs to be careful of when they rebase. Rebasing should occur before the feature branch is merged into master. If you start rebasing master, if another developer branches off of a commit that does not exist anymore, changes can be lost.

On the other hand, when rebasing in feature branches, everything is fine because the developer is rebasing their own code. If a developer were to rebase in master, all traceability is lost because commits are being rewritten. If the rebase is done by another developer who did not commit those changes, the "author" of the change is now lost.

## Resources

These are the resources used to make this guide:

* [Trunk Based Development](https://trunkbaseddevelopment.com/)
* [Git Flow vs Github Flow](https://lucamezzalira.com/2014/03/10/git-flow-vs-github-flow/)
* [Feature Flags](http://featureflags.io/)
* [How to Write a Git Commit Message](https://chris.beams.io/posts/git-commit/)
* [Git Interactive Rebase, Squash, Amend and Other Ways of Rewriting History](https://robots.thoughtbot.com/git-interactive-rebase-squash-amend-rewriting-history)
* [Merging vs. Rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)
* [Git Anti Patterns How To Mess Up With Git And Love It Again](https://www.youtube.com/watch?v=Ey1t5omqfYA&t=20s) tought me how to rebase and use Git Templates. Video brought to us by Lemi Orhan Ergin who presented at Devoxx 2017.