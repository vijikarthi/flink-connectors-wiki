# Table of contents

 - [Introduction](#introduction)
 - [Issues and Pull Requests](#issues-and-pull-requests)
 - [Signing your commits](#signing-your-commits)

# Introduction

Pravega really encourages anyone to contribute and to make our system better. We ask contributors, however, to read this guide carefully and follow the established guidelines. We don't claim this is perfect, so these guidelines will change over time as we feel that it is not complete or appropriate. Suggestions on how to improve this guide for contributors are also welcome.

# Issues and Pull requests <a name="issue"></a>

To produce a pull request against Pravega, follow these steps:

- **Create an issue:** Create an issue and describe what you are trying to solve. It doesn't matter whether it is a new feature, a bug fix, or an improvement. **All pull requests need to be associated to an issue.** See more here: [Creating an issue](#creating-an-issue)

- **Issue branch:** Create a new branch on your fork of the repository. Typically, you need to branch off master, but there could be exceptions. To branch off `master`, use `git checkout master; git checkout -b <new-branch-name>.`

- **Push the changes:** To be able to create a pull request, push the changes to origin: `git push --set-upstream origin <new-branch-name>`. I'm assuming that `origin` is your personal repo, e.g., `fpj/flink-connectors.git.`

- **Branch name:** Use the following pattern to create your new branch name: `issue-number-description`, e.g., `issue-1023-reformat-testutils`.

- **Create a pull request:** Github gives you the option of creating a pull request. Give it a title following this format `Issue ###: Description`, e.g., `Issue 1023: Reformat testutils`. Follow the guidelines in the description and try to provide as much information as possible to help the reviewer understand what is being addressed. It is important that you try to do a good job with the description to make the job of the code reviewer easier. A good description not only reduces review time, but also reduces the probability of a misunderstanding with the pull request.

- **Merging:** If you are a committer, see this document: [Guidelines for Committers](https://github.com/pravega/flink-connectors/wiki/guidelines-for-committers)

Another important point to consider is how to keep up with changes against the base the branch (the one your pull request is comparing against). Let's assume that the base branch is master. To make sure that your changes reflect the recent commits, we recommend that you `rebase` frequently. The command we suggest you use is:

```

git pull --rebase upstream master
git push --force origin <pr-branch-name>
```
## Very important: in the above, I'm assuming that:

- `upstream` is `pravega/flink-connectors.git`
- `origin` is `youraccount/flink-connectors.git`

The `rebase` might introduce conflicts, so you better do it frequently to avoid outrageous sessions of conflict resolving.

# Creating an issue

When creating an issue, there are two important parts: title and description. The title should be succinct, but give a good idea of what the issue is about. Try to add all important keywords to make it clear to the reader. For example, if the issue is about changing the log level of some messages in the segment store, then instead of saying "Log level" say "Change log level in the segment store". The suggested way includes both the goal where in the code we are supposed to do it.

For the description, there three parts:

- _Problem description:_ Describe what it is that we need to change. If it is a bug, describe the observed symptoms. If it is a new feature, describe it is supposed to be with as much detail as possible.

- _Problem location:_ This part refers to where in the code we are supposed to make changes. For example, if it is bug in the client, then in this part say at least "Client". If you know more about it, then please add it. For example, if you that there is an issue with `SegmentOutputStreamImpl`, say it in this part.

- _Suggestion for an improvement:_ This section is designed to let you give a suggestion for how to fix the bug described in the Problem description or how to implement the feature described in that same section. Please make an effort to separate between problem statement (Problem Description section) and solution (Suggestion for an improvement).

We next discuss how to create a pull request.

# Creating a pull request

When creating a pull request, there are also two important parts: title and description. The title can be the same as the one of the issue, but it must be prefixed with the issue number, e.g.:

```
Issue 724: Change log level in the segment store
```
The description has four parts:

- **Changelog description**: This section should be the two or three main points about this PR. A detailed description should be left for the _What the code does section_. The two or three points here should be used by a committer for the merge log.

- **Purpose of the change:** Say whether this closes an issue or perhaps is a subtask of an issue. This section should link the PR to at least one issue.

- **What the code does:** Use this section to freely describe the changes in this PR. Make sure to give as much detail as possible to help a reviewer to do a better job understanding your changes.

- **How to verify it:** For most of the PRs, the answer here will be trivial: the build must pass, system tests must pass, visual inspection, etc. This section becomes more important when the way to reproduce the issue the PR is resolving is non-trivial, like running some specific command or workload generator.

# Signing your commits

## Using the git command line

Use either `--signoff` or `-s` with the commit command. See this [document](https://probot.github.io/apps/dco/) for an example.

Make sure you have your user name and e-mail set. The `--signoff | -s` option will use the configured user name and e-mail, so it is important to configure it before the first time you commit. Check the following references:

- Setting up your github user name [reference](https://help.github.com/articles/setting-your-username-in-git/)
- Setting up your e-mail address [reference](https://help.github.com/articles/setting-your-commit-email-address-in-git/)

## Using InteliJ

Intellij has a checkbox in the top-right corner of the Commit Dialog named `Sign-off commit`. Make sure you also enter your correct email address in the `Author` box right above it.

## Using Eclipse

The EGit plugin supports signing commits. See [their instructions](https://wiki.eclipse.org/EGit/User_Guide/One_page). To enable it by default, in Window -> Preferences -> Team -> Git -> Commiting under "Footers" check "Insert Signed-off-by".

## Using Sourcetree

When you commit right above where you enter the commit message on the far right, the "Commit options..." pull down has a "Sign Off" option. Selecting it will enable it for the commit. There is no configuration to force it always to be on.

# I have forgot to sign-off a commit in my pull request
There are a few ways to fix this, one such a way is to squash commits into one that is signed off. Use a command like:
```
git rebase --signoff -i <after-this-commit>
```
where `<after-this-commit>` corresponds to the last commit you want to maintain as is and follow the instructions [here](https://git-scm.com/docs/git-rebase#_interactive_mode).
