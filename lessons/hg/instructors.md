---
layout: page
title: Version Control with Mercurial
subtitle: Instructor's Guide
---
## Legend

Wolfman and Dracula have been hired by Universal Missions
(a space services spin-off of Euphoric State University)
to figure out where the company should send its next planetary lander.
They want to be able to work on the plans at the same time,
but they have run into problems doing this in the past.
If they take turns,
each one will spend a lot of time waiting for the other to finish,
but if they work on their own copies and email changes back and forth
things will be lost, overwritten, or duplicated.

The solution is to use [version control](reference.html#version-control)
to manage their work.
Version control is better than mailing files back and forth because:

*   Nothing that is committed to version control is ever lost.
    This means it can be used like the "undo" feature in an editor,
    and since all old versions of files are saved
    it's always possible to go back in time to see
    exactly who wrote what on a particular day,
    or what version of a program was used to generate a particular set of results.
*   It keeps a record of who made what changes when,
    so that if people have questions later on
    they know who to ask.
*   It's hard (but not impossible) to accidentally overlook or overwrite someone's changes:
    the version control system automatically notifies users
    whenever there's a conflict between one person's work and another's.

This lesson shows how to use
a popular open source version control system called [Mercurial](http://mercurial.selenic.com/)
(also known as `hg`).
It is widely used,
both because it's easy to set up
and because of a hosting site called [Bitbucket](http://bitbucket.org/).
No matter which version control system you use,
the most important thing to learn is not the details of their more obscure commands,
but the workflow that they encourage.


## Overall

*   Time estimate @douglatornell: 2 hours 45 minutes

Version control may be the most important topic that we teach.
We're teaching Mercurial because it offers a consistent user interface,
and the basic workflow requires only a few easy to remember commands.

There are lots of advanced features in Mercurial and other distributed version control systems such as branching,
hashes,
rebasing,
etc.
that we don't teach.
The goal is to introduce the basics so that the learners leave the workshop able to create and use repositories for themselves with confidence,
and that they are aware that version control can also be used to collaborate with other people.

In other words,
try to convince the learners that version control is useful for researchers
working alone or in teams because it is

- a better way to "undo" changes,
- a better way to collaborate than mailing files back and forth, and
- a better way to share your code and other scientific work with the world.

## Teaching Notes

*   Make sure the network is working *before* starting the [Collaborating](02-collab.html) section of this lesson.
    [A Better Kind of Backup](01-backup.html) focuses on individual use of Mercurial on and can be done without network access.
    [Conflicts](03-conflict.html) can also be taught without network access and Bitbucket by creating 2 clones of the repository and pulling changes between them.

*   Drawings are particularly useful in this lesson:
    if you have a whiteboard,
    use it!

*   Version control is usually not the first subject in a workshop,
    so get learners to create a Bitbucket account after the session before.

*   [TortoiseHg](http://tortoisehg.bitbucket.org/) is the recommended means of installing Mercurial for Windows learners.
    The workshop installation instructions should include that.

*   We don't use a Mercurial GUI in these notes because we want learners to understand what commands are being run.
    That said,
    instructors may want to demo the [TortoiseHg](http://tortoisehg.bitbucket.org/) GUI
    (which is available for Windows, OS/X, and Linux)
    on their desktop at some point during this lesson.

*   The [Conflicts](03-conflict.html) section of the lesson uses the [KDiff3](http://kdiff3.sourceforge.net/) graphical diff/merge tool.
    The workshop installation instructions should include directions for OS/X and Linux users to install KDiff3.
    For Windows users it is bundled with TortoiseHg.

*   When appropriate, explain that we teach Mercurial rather than CVS,
    Subversion,
    or Git primarily because of its consistent command-line interface,
    easy to learn basic workflow,
    and Bitbucket's policy of providing unlimited collaboration in private repositories for academic users.
    CVS and Subversion are now seen as legacy systems.
    Git is also very popular,
    largely because of the GitHub service.
    The differences between Mercurial and Git are largely syntactic,
    so learning one provides the conceptual framework to use either.


## [A Better Kind of Backup](01-backup.html)

*   Ask, "Who uses 'undo' in their editor?"
    All say "Me".
    'Undo' is the simplest form of version control.

*   Give learners a five-minute overview of what version control does for them
    before diving into the watch-and-do practicals.
    Most of them will have tried to co-author papers by emailing files back and forth,
    or will have biked into the office only to realize that the USB key with last night's work is still on the kitchen table.
    Instructors can also make jokes about directories with names like "final version",
    "final version revised",
    "final version with reviewer three's corrections",
    "really final version",
    and,
    "come on this really has to be the last version" to motivate version control as a better way to collaborate and as a better way to back work up.

**Setting Up Mercurial**

*   We suggest instructors and students use `nano` as the text editor for this lessons because

    - it runs in all three major operating systems,
    - it runs inside the shell (switching windows can be confusing to students), and
    - it has shortcut help at the bottom of the window.

    Please point out to students during setup that they can and should use another text editor if they're already familiar with it.

*   When setting up Mercurial,
    be very clear what learners have to enter:
    it is common for them to edit the instructor's details
    (e.g. email).


**Creating a Repository** and **Changing a File**

*   When you do `hg status`,
    Mac users may see a `.DS_Store` file showing as untracked.
    This a file that Mac OS creates in each directory.


**Tracking Changes to Files**

*   It's important that learners do a full commit cycle by themselves
    (make changes, `hg diff`, and `hg commit`).
    The "`bio` repository" challenge does that.

*   This is a good moment to show a diff with the KDiff3 graphical diff tool.
    If you skip it because you're short on time,
    show the graphical diff view of a commit on Bitbucket in the [Collaborating](02-collab.html) section.


**Exploring History** and **Recovering Old Versions**

*   Mention that you can revert more than one file at a time by listing the file names one after the other in the `hg revert` command,
and that shell wildcard characters can also be used.


## [Collaborating](02-collab.html)

*   Make it clear that Mercurial and Bitbucket are not the same thing:
    Mercurial is an open source version control tool,
    Bitbucket is a company that hosts Mercurial repositories on the web and provides a web interface to interact with repos that they host.

*   If your learners are advanced enough to be comfortable with SSH,
    tell them they can use keys to authenticate on GitHub instead of passwords,
    but don't try to set this up during class: it takes too long,
    and is a distraction from the core ideas of the lesson.

*   It is very useful to draw a diagram showing the different repositories involved.

*   Role playing between two instructors can be effective when teaching the collaboration and conflict sections of the lesson.
    One instructor can play the role of the repository owner,
    while the second instructor can play the role of the collaborator.
    If it is possible,
    try to use two projectors so that the computer screens of both instructors can be seen.
    This makes for a very clear illustration to the students as to who does what.

*   It is also effective to pair up students during this lesson and assign one member of the pair to take the role of the owner and the other the role of the collaborator.
    In this setup,
    challenges can include asking the collaborator to make a change,
    commit it,
    and push the change to the remote repository so that the owner can then retrieve it,
    and vice-versa.
    The role playing between the instructors can get a bit "dramatic" in the conflicts part of the lesson if the instructors want to inject some humor into the room.

*   If you don't have two projectors,
have two instructors at the front of the room.
    Each instructor does their piece of the collaboration demonstration on their own computer and then passes the projector cord back and forth with the other instructor when it's time for them to do the other part of the collaborative workflow.
    It takes less than 10 seconds for each switchover,
    so it doesn't interrupt the flow of the lesson.
    And of course it helps to give each of the instructors a different-colored hat,
    or put different-colored sticky notes on their foreheads.

*   If you're the only instructor,
    the best way to create is clone the two repos in your Desktop,
    but under different names,
    e.g., pretend one is your computer at work:

    ~~~{.input}
    $ hg clone https://bitbucket.org/vlad/planets planets-at-work
    ~~~

*   It's very common that learners mistype the remote alias or the remote URL
    when adding a remote,
    so they cannot `push`.
    You can diagnose this with `hg paths` and checking carefully for typos.
    To fix typos you can open the repo `.hg/hgrc` file for editing with `hg config --local`.

*   Before cloning the repo, be sure that nobody is inside another repo.
    The best way to achieve this is moving to the `Desktop` before cloning:
    `cd && cd Desktop`.

*   If both repos are in the `Desktop`,
    have them to clone their collaborator repo under a given directory using a second argument:

    ~~~{.input}
    $ hg clone https://bitbucket.org/vlad/planets vlad-planet`
    ~~~

*   Conflicts,
    sometimes weird,
    will start to arise. Stay tight: conflicts are next.


## [Conflicts](03-conflict.html)

*   Explain that conflict are not a very common occurrence.
    Mercurial is good at merging changes made by different people,
    or changes that you make in different clones of a repo;
    e.g. on your office workstation,
    on your laptop at home,
    on a lab computer,
    or on a remote HPC or cloud platform.
    A conflict arises when changes have been made to the same lines in a file or to binary files;
    Mercurial can't figure out which of the conflicting version to choose so it makes you decide.

*   Expect the learners to make mistakes.
    Expect *yourself* to make mistakes.
    This happens because it is late in the lesson and everyone is tired.

*   If you're the only instructor,
    the best way to create a conflict is:

    - Clone your repo in a different directory,
      pretending is your computer at
      work:
      `hg clone https://bitbucket.org/vlad/planets planets-at-work`.
    - At the office,
      you make a change,
      commit and push.
    - At your laptop repo,
      you (forget to pull and) make a change,
      commit and try to push.
    - `hg pull` now and show the conflict.

*   The commands `hg heads`,
    `hg log --graph` or `hg log -G`,
    and `hg summary`
    all provide views of the repository state that can be helpful in explaining conflicts and how they are resolved.

*   Use `hg merge --tool=kdiff3` to ensure that the KDiff3 GUI diff/merge tool is launched to handle resolution of the conflict.

## [Open Science](04-open.html)

**Licensing**

We teach about licensing because questions about who owns what,
or can use what,
arise naturally once we start talking about using public services like
Bitbucket to store files.
Also,
the discussion gives learners a chance to catch their breath after what is often a frustrating couple of hours.


**Hosting**

A common concern for learners is having their work publicly available on
Bitbucket.
While we encourage open science,
sometimes private repos are the only choice.
It's always interesting to mention the options to have web-hosted private repositories.
