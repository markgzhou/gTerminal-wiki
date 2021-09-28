# Git

## Pushing to Multiple Git Repos
Remotes
=====

Suppose your git remotes are set up like this::

    git remote add github git@github.com:myfirstgit/my-project.git
    git remote add bb git@bitbucket.org:secondgit/my-project.git

The ``origin`` remote probably points to one of these URLs.

Remote Push URLs
====

To set up the push URLs do this::

    git remote set-url --add --push origin git@github.com:myfirstgit/my-project.git
    git remote set-url --add --push origin git@bitbucket.org:secondgit/my-project.git

It will change the ``remote.origin.pushurl`` config entry. Now pushes
will send to both of these destinations, rather than the fetch URL.

Check it out by running::

    git remote show origin


Per-branch
==========

A branch can push and pull from separate remotes. This might be useful
in rare circumstances such as maintaining a fork with customizations
to the upstream repo. If your branch follows ``github`` by default::

    git branch --set-upstream-to=github next_release

(That command changed ``branch.next_release.remote``.)

Then git allows branches to have multiple ``branch.<name>.pushRemote``
entries. You must edit the ``.git/config`` file to set them.


Pull Multiple
=============

You can't pull from multiple remotes at once, but you can fetch from
all of them::

    git fetch --all

Note that fetching won't update your current branch (that's why 
``git-pull`` exists), so you have to merge -- fast-forward or otherwise.

For example, this will octopus merge the branches if the remotes got
out of sync::

    git merge github/next_release bb/next_release
