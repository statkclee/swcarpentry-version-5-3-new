---
layout: page
title: Version Control with Mercurial
subtitle: Reference
---
## A Better Kind of Backup

*   Create a `~/.hgrc`
    (or `%USERPROFILE%\Mercurial.ini` on Windows) to configure a user name,
    email address, editor, and other preferences once per machine.
*   `hg init` initializes a repository.
*   `hg status` shows the status of a repository.
*   Files are stored in a project's working directory (which users see),
    and the local repository (where committed snapshots are permanently recorded).
*   `hg add` tells Mercurial to track files.
*   `hg commit` creates a snapshot of the changes to 1 or more files in the local repository.
*   Always write a log message when committing changes.
*   `hg diff` displays differences between revisions.
*   `hg revert` recovers old versions of files.
*   The `.hgignore` file tells Mercurial what files to ignore.

## Collaborating

*   A local Mercurial repository can be connected to one or more remote repositories.
*   Use the HTTPS protocol to connect to remote repositories
    until you have learned how to set up SSH.
*   `hg push` copies changes from a local repository to a remote repository.
*   `hg pull` copies changes from a remote repository to a local repository.
*   `hg clone` copies a remote repository to create a local repository
    with a remote called `default` automatically set up.

## Conflicts

*   Conflicts occur when two or more people change the same file(s) at
    the same time.
*   The version control system does not allow people to blindly
    overwrite each other's changes. Instead, it highlights conflicts
    so that they can be resolved.

## Open Science

*   Open scientific work is more useful and more highly cited than closed.
*   People who incorporate GPL'd software into theirs must make theirs open;
    most other open licenses do not require this.
*   The Creative Commons family of licenses allow people to mix and match
    requirements and restrictions on attribution,
    creation of derivative works,
    further sharing,
    and commercialization.
*   People who are not lawyers should not try to write licenses from scratch.
*   Projects can be hosted on university servers,
    on personal domains,
    or on public forges.
*   Rules regarding intellectual property and storage of sensitive information apply
    no matter where code and data are hosted.

## Glossary

changeset
:   A group of changes to one or more files
    that are [committed](#commit) to a [version control](#version-control) [repository](#repository)
    in a single operation.

clone
:   To make a local copy of a [version control repository](#repository).
    See also: [fork](#fork).

commit
:   To record the current state of a set of files (a [changeset](#changeset))
    in a [version control](#version-control) [repository](#repository).

conflict
:   A change made by one user of a [version control system](#version-control)
    that is incompatible with changes made by other users.
    Helping users [resolve](#resolve) conflicts is one of version control's major tasks.

fork
:   To [clone](#clone) a [version control](#version-control) [repository](#repository)
    on a server.

HTTP
:   The Hypertext Transfer [Protocol](#protocol) used for sharing web pages and other data
    on the World Wide Web.

infective license
:   A license such as the [GPL](http://opensource.org/licenses/GPL-3.0)
    that compels people who incorporate material into their own work
    to place similar sharing requirements on it.

Internet Service Provider (ISP)
:   A company or other organization that provides access to
    the Internet. Many ISPs provide hosting services for websites.

merge
:   To reconcile two sets of changes to a [repository](#repository).

protocol
:   A set of rules that define how one computer communicates with another.
    Common protocols on the Internet include [HTTP](#http) and [SSH](#ssh).

remote
:   A version control [repository](#repository) other than the current one
    that the current one is somehow connected to or mirroring.

repository
:   A storage area where a [version control](#version-control) system
    stores old [revisions](#revision) of files and information about who changed what, when.

resolve
:   To eliminate the [conflicts](#conflict) between two or more incompatible changes to a file or set of files
    being managed by a [version control](#version-control) system.

revision
:   A recorded state of a [version control](#version-control) [repository](#repository).

SSH
:   The Secure Shell [protocol](#protocol) used for secure communication between computers.

timestamp
:   A record of when a particular event occurred.

version control
:   A tool for managing changes to a set of files.
    Each set of changes creates a new [revision](#revision) of the files;
    the version control system allows users to recover old revisions reliably,
    and helps manage conflicting changes made by different users.
