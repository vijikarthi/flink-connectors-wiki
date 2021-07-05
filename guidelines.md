# Guidelines for Committers

We currently use the **squash** and **merge** option to merge pull requests. The whole idea is that we conflate all the changes that have been committed to the pull request branch into a single commit that reflects the overall changes needed to solve a single issue. That's one reason why we associate pull requests with issues, as this way we have a single entry in the git log for each resolved issue, describing the precise changes that have been implemented to solve the issue.

# Checks

We have four checks in place at the moment for pull requests:

1. Developer Certificate of Origin (DCO)
2. Code coverage
3. Travis build
4. Branch conflicts

None of them are currently required to enable the merge, but the DCO check will soon be mandatory.

# Developer Certificate of Origin (DCO)

It is very important that all individual commits of a pull requests have been signed off. The DCO plugin verifies it and fails the check if there is at least one unsigned commit.

When merging the pull request, we still want to preserve the signed-off-by line, but we only need to keep one, like this:

```
commit aae930a4138cce4a6bc6e34db3de318deeb06503
Author: Sandeep <sandeep.shridhar@emc.com>
Date:   Tue Oct 3 19:27:45 2017 +0530

    Issue 1688: Close transport connections used by controller client after use. (#1914)

    * Ensures that io.pravega.client.stream.impl.Controller closes resources (transport connections)
    after use.

    Signed-off-by: shrids <sandeep.shridhar@emc.com>
```
# Code coverage

We currently use **codecov**. It gives a good indication of code coverage and try hard to keep the coverage of tests high, but we have found ourselves in situations in which the code coverage indicator was not giving an accurate estimate. Consequently, we look at it and take it seriously, but we don't use it in a binary way. We try to judicious about the report results.

# Travis builds

It is very important to make sure that the travis build passes before merging a pull request. In the case there is a build failure and as a committer you still feel that it can be merged, it is important that you leave a note in the pull request explaining the reason why you decided to merge despite the build failure.

Here is one scenario that justifies a pull request _P_ being merged despite a build failure. Say that there is a know test case that is failing and has an issue open. If that's the reason for the test failure, then the committer may decide to merge, although the preferred way would still be to fix the test case failure with a different pull request before merging this pull request _P_.

# Branch conflicts

Branch conflicts will block the merge, they need to be fixed before merging is enabled and the list of conflicts is presented as part of the check results.

# Merging a pull request

In three steps:

1. **Checks:** Make sure that all relevant checks have passed, see the previous section. If they didn't pass, then don't be lazy, help the contributor understand what has gone wrong.

2. **Merge Title:** Make sure the merge title is the same as the pull request one. Typically, the github UI will give you the pull request title as the merge title, but in the one case when the pull request has a single commit, the github UI will replace the merge title with the title of the first commit. In this latter case, you will need to copy and paste to fix it.

3. **Merge text body:** This is the box right under the title. It is typically populated with a list, where each bullet corresponds to a commit title. We **remove** that list and **replace** with the change log description in the pull request description above at the top of the pull request conversation tab. Sometimes the description has typos, it is not well explained, or simply too long. It is at the discretion of the committer whether to change that text or not. If you change it, then make sure you know what you are doing. **Make sure to keep exactly one signed-off-by line to indicate that the developer complied with the DCO requirement.**

# An example

Here is a screenshot to show how the text box should look like:

![Image Not Found](https://github.com/pravega/wiki-images/raw/master/pravega/guidelines-for-committers/pr-merge-screenshot.png)
