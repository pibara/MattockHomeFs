MattockHomeFs Home directory integrity for worker processes
===========================================================

MattockHomeFs is a spin-off of the MattockFS forensic file-system. While it's primary goal is to provide home-dir integrity to computer forensic worker processes operating within the context of a MattockFS based computer forensic framework, the set-up of MattockHomeFs is such that it should be usable in many other contexts.

MattockFsHome will by default be mounted under /var/mattock/home and will provide one of a number of views to processes accesing it:

* The user view
* The parent view
* The child view

User view
=========

In this view, the directory list will show:

* An empty file named 'home.ctl'
* A symbolic link for each MattockHomeFs registered pid that has the same uid as the process accessing MattockHomeFs. This symlink points to a directory under /var/mattock/work/
* A README file explaining the use of the direcory.

The home.ctl file has an extended attribute named 'register' that may be set to '1' by a process in order to change it's view from user-view to parent-view.


Parent view
===========

This view is the view for processes that invoked 'register' earlier.
In this view, the directory is fully read/write accessible to the process. The empty file named 'home.ctl' is also there and can not be overwritten, deleted or in any way changed. The home.ctl file now has an extended attribute named 'unregister' that may be set to '1' by a process in order to change it's view from parent-view back to user-view. Doing so shall delete the whole underlaying parent-view directory tree.


The Child view.
===============

If a process did not itself invoke 'register' but has a parent or further still connected ancester in the process tree that did invoke register, than that process will see the child view.

The child view is virtually identical to the parent view with the exception that in the child view, the 'home.ctl' has no 'unregister' attribute. 

/var/mattock/work
=================

MattockHomeFs is a simple overlay file-system where for parent and Child mode, /var/mattock.home is (mostly) mapped to /var/mattock/work/$PID. The data in /var/mattock/work/$PID will be owned by the mtckhome user, but will have access rights set to world readable.

