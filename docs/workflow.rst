=========================================
Robust GIT workflow for research projects
=========================================

Here we present a GIT workflow which is very robust. It is based on the referenced pages of `Florent Lebreton`_. We have tested this workflow thoroughly and may say it is really simple an stable. We have included some simplifications at the end through git-friendly shell scripts.

Rules
=====

To handle this, we have set some simple rules:

1. Only ONE maintainer, who manage GIT repository and releases 
2. Never commit directly on master 
3. Never rebase master on any branch 
4. Persevere in the planned workflow

Workflow
========

There are Four kind of brach:

* Master
* Development branches (deleted after merge)
* Stable branches (not deleted)
* gh-pages (for documentation only)

Master branch
=============

Branch master is the common trunk and simply contains all the files of the next release. Since we don't work directly on it, it should evolves only with merges.

  .. figure:: images/general/image1.png
     :align: left
     :scale: 100%
     :alt: Master branch.
     
     *Figure 1: Master branch.*

Development branches
====================

When you start a new feature or a bugfix, you create a new branch from master HEAD

``$ (master) git checkout -b featureA``
``$ (featureA) ...“You work and modify all you need”...``
``$ (featureA) git add -A``
``$ (featureA) git commit -a -m "featureA part 1"``
``$ (featureA) git commit -a -m "featureA part 2"``
|
  .. figure:: images/general/image2.png
     :align: left
     :scale: 100%
     :alt: Development branches.
     
     *Figure 2: Development branches.*
|
Follow branch master evolution and regularly ensure your code still works, by rebasing branch featureA on branch master.

``$ (featureA) git rebase master``
When developments are done (commits fa1 / fa2 in schema below), you do a last rebase.
If tests pass on development branch after rebase, they should pass on master after merge, so **you ensure that branch "master" is always working well**

  .. figure:: images/general/image3.png
     :align: left
     :scale: 100%
     :alt: Development branches rebase master.
     
     *Figure 3: Development branches rebase master.*
 
 
The maintainer can now merge this branch in master without big conflicts. Use no-ff option to force a merge commit, so **history can stay clearly readable** (to see where the branch has started and where it has been merged).

``$ (featureA) git checkout -b master``
``$ (master) git merge --no-ff featureA``

  .. figure:: images/general/image4.png
     :align: left
     :scale: 100%
     :alt: Development branches merge.
     
     *Figure 4: Development branches merge.*

Now that the branch has been merged, remove the development branch.

``$ (master) git branch -d featureA``
``$ (master) git push origin :featureA``

Stable branches
===============

When you prepare a release, tag the branch master, then start a stable branch.

``$ (master) git tag 1.0``
``$ (master) git checkout -b stable1.0``
``$ (stable1.0) git push origin stable1.0``

This branch may be deployed on different servers.

While development goes on, you possibly have to do some hotfixes (for example: commit hf1 in schema below), that must be sent in production quickly. These hotfixes are done directly on the stable branch.

  .. figure:: images/general/image5.png
     :align: left
     :scale: 100%
     :alt: Development branches merge.
     
     *Figure 5: Stable branch.*

Regularly, the maintainer merges stable branch in master to bring back these commits. This action is particularly important before the next release.

``$ (stable1.0) git commit -a -m "hotfix 1"``
``$ (stable1.0) git rebase maste``
``$ (stable1.0) git checkout -b master``
``$ (master) git merge --no-ff stable1.0``

A complete history example
==========================

  .. figure:: images/general/image6.png
     :align: left
     :scale: 100%
     :alt: Development branches merge.
     
     *Figure 6: Stable branch.*

Git-simple to simplify your life
================================

Git sometimes requires typing two or three commands just to execute something basic like fetching new code. `git-simple`_ adds a few new commands — `gremote`, `gpull`, `gpush`, `gbranch`, `gmerge` and `gpublish` which:

* **gremote** Creates a remote Github repository from the current local directory;
* **gmerge** Tries to merge a local branch into the current branch;
* **gpush** Sends your local branch changes to the remote branch;
* **gpull** Pulls remote changes using rebase & tries to rebundle;
* **gbranch** Creates and tracks remote branches if they are available;
* **gpublish** Publish your sphinx docs on Github gh-pages;

*Less time fighting Git.*

Here is the complet workflow with git and git-simple for both branchs (features and stables). gh-pages branch is actualized with ``gpublish``.

.. csv-table::
    :header: Development Branch, Stable Branch

    " ", ``$ (master) git tag 1.0``
    ``$ (master) git checkout -b featureA``, ``$ (master) git checkout -b stable1.0``
    " ",``$ (stable1.0) git push origin stable1.0``


References
==========
.. _`Florent Lebreton`: http://fle.github.io/
.. _`git-simple`: https://github.com/parrondo/git-simple
