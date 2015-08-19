---
layout: page
title: Version Control with Mercurial
subtitle: Discussion
---

## Frequently Asked Questions

People often have questions about Mercurial beyond the scope of the core material.
Students who have completed the rest of the lessons might find value in looking through the following topics.

Note that since this material isn't essential for basic Mercurial usage,
it won't be covered by the instructor.


## More Advanced Mercurial Configuration

In the [Setting Up](01-backup.html#setting-up) section we edited a Mercurial configuration file in our home directory called `$USERPROFILE/Mercurial.ini` or `~/.hgrc`.
You can quickly open that file for editing with the command `hg config --edit`.

If you want to know more about the many configuration options available you can use `hg help config` or visit [the Mercurial config documenttation](http://www.selenic.com/mercurial/hgrc.5.html).

One configuration option that can be useful is adding aliases.
These are like shorcuts for longer `hg` commands.
For example,
if you want to create an `hg latest` command to show only the five most recent changesets,
you can edit your config file with `hg config --edit` and add a section that looks like:

~~~ {.bash}
[aliases]
latest = log --limit 5
~~~

Each repository can also have its own configuration file stored at `.hg/hgrc`.
That file is where the path to a remote location that the repo may have been cloned from is stored.
You can open that file for editing with the command `hg config --local`.


## Non-text Files

Recall when we discussed [Conflicts](03-conflict.html)
there was a challenge that asked:

> What does hg do
> when there is a conflict in an image or some other non-textual file
> that is stored in version control?

We will now revisit this in more detail.

Many people want to version control non-text files, such as images, PDFs and Microsoft Office or LibreOffice documents.
It is true that Mercurial can handle these filetypes (which fall under the banner of "binary" file types).
However, just because it *can* be done doesn't mean it *should* be done.

Much of Mercurial's magic comes from being able to do line-by-line comparisons ("diffs") between files.
This is generally easy for programming source code and marked up text.
For non-text files, a diff can usually only detect that the files have changed
but can't say how or where.

This has various impacts on Mercurial's performance and will make it difficult to
compare different versions of your project.

For a basic example to show the difference it makes,
we're going to go see what would have happened if Dracula had tried
using outputs from a word processor instead of plain text.

Create a new directory and go into it:

~~~ {.bash}
$ mkdir planets-nontext
$ cd planets-nontext
~~~

Use a program such as Microsoft Word or LibreOffice Writer to create a new document.
Enter the same text that we began with before:

~~~ {.output}
Cold and dry, but everything is my favorite color
~~~

Save the document into the `planets-nontext` directory with the name of `mars.doc`.
Back in the terminal, run the usual commands for setting up a new Mercurial repository:

~~~ {.bash}
$ hg init
$ hg add mars.doc
$ hg commit -m "Starting to think about Mars"
~~~

Then make the same changes to `mars.doc` that we (or Vlad) previously made to `mars.txt`.

~~~ {.output}
Cold and dry, but everything is my favorite color
The two moons may be a problem for Wolfman
~~~

Save and close the word processor.
Now see what Git thinks of your changes:

~~~ {.bash}
$ hg diff
~~~
~~~ {.output}
diff -r 0f3937fd3863 mars.doc
Binary file mars.doc has changed
~~~

Compare this to the earlier `git diff` obtained when using text files:

~~~ {.output}
diff -r 72ab25fa99a1 mars.txt
--- a/mars.txt  Mon Apr 14 14:41:58 2014 -0400
+++ b/mars.txt  Mon Apr 14 15:48:53 2014 -0400
@@ -1,1 +1,2 @@
 Cold and dry, but everything is my favorite color
+The two moons may be a problem for Wolfman
~~~

Notice how plain text files give a much more informative diff.
You can see exactly which lines changed and what the changes were.

An uninformative `hg diff` is not the only consequence of using Mercurial on binary files.
However, most of the other problems boil down to whether or not a good diff is possible.

This isn't to say you should *never* use Mercurial on binary files.
A rule of thumb is that it's okay if the binary file won't change very often,
and if it does change,
you don't care about merging in small differences between versions.

Another thing to note about tracking binary files in Mercurial is that a new copy of the whole file is stored whenever a change to a binary file is committed.
This can lead to the repository growing very large if the binary files are large,
and/or numerous,
and/or changed frequently.

We've already seen how a word processed report will fail this test.
An example that passes the test is a logo for your organization or project.
Even though a logo will be stored in a binary format such as `jpg` or `png`,
you can expect it will remain fairly static through the lifetime of your repository.
On the rare occasion that branding does change,
you will probably just want to replace the logo completely rather than merge little differences in.
