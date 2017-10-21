================================
sel - simple administration tool
================================

SEL is a technology demonstrator and kind of sketch-program, created for
Common Output Format project, see: <https://github.com/aniou/cof/wiki/Draft>.


Installation instructions 
=========================

NetBSD 8
--------

As root:

.. code:: 
  
    # pkg_add python36
    # pkg_add py36-pip
    # pkg_add py36-six 

As user:

.. code:: 
 
    $ pip3.6 install --user parsimonious
    $ pip3.6 install --user termcolor


Ubuntu 16.04
------------



As root:

.. code::

    # apt-get install python3 python3-six python3-pip python3-termcolor

As user:

.. code::

    $ pip3 install parsimonius

Then change shebang in ``sel`` to ``#!/usr/bin/python3 -tt``

Usage
=====

In general, ``sel`` invocation uses three kinds of parameters:
flags, selectors, filters.

Currently ``sel`` supports two different option sets as well as two 
different filter grammars. 

.. code::
 
   $ sel -h
   Usage(1): sel [-qh] [<column>, [<column>, ...]]    [where <filter>]
   Usage(2): sel [-qh] -f [<column>, [<column>, ...]] [-w <filter>]

                 ^      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^^^^^^^
                 flag   column selector               filter


Parametrs
=========

There are two forms of parameters - "dashless ones" are probably more
suitable for passing arguments when filter is called from another app
and parameters are passed via additional option, for example:

.. code::
 
    df --of '1,2,Used'


===============   ==================   ================================
Version1          Version2             Role

``-q``            ``-q``               Don't print meta-data or headers

``-f <column>``   ``<column>``         Selects columns to print

``-w <filter>``   ``where <filter>``   Conditionals for rows        
===============   ==================   ================================

Example:

.. code::
  
    $ df | sel 1,2,Used,Used where Used -eq 0
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used Used      
    udev       1837416     0    0         
    tmpfs      1857800     0    0         

Filter grammar
==============

There also two filter grammars. Unfortunately, in shell environment 
convenient  operators like ``<``, ``>``, ``>=``, ``(`` or ``)`` are
cumbersome in use and may cause subtle bugs.

There are some rules:

- column names are always case-insensitive

- literals are not quoted. That makes impossible to write someone 
  like comparison between two columns ``when Used -gt Free`` but is
  a lot easier when we need a ``when Mounted -starts /opt``.

===============   ==================   ================================
Version1          Version2             meaning

``-a`` or ``,``   ``and`` or ``,``     AND

``-o``            ``or``               OR

``-eq``           ``eq``               equals

``-gt``           ``gt``               greater than

``-lt``           ``lr``               less than

``-le``           ``le``               less or equal than

``-gt``           ``gt``               greater or equal than

``-starts``       ``starts``           starts with

``-ends``         ``ends``             ends with

``-has``          ``has``              contains

``[`` and ``]``   ``[`` and ``]``      brackets for grouping statements

``NOT``           ``NOT``              negation (XXX: not well tested)
===============   ==================   ================================

Some examples:

.. code::

    $ cat samples/df.txt | ./sel 1,2,3 where Used -gt 0 -a Filesystem -eq tmpfs
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used  
    tmpfs      371560      5956  
    tmpfs      1857800     56916 
    tmpfs      5120        4     
    tmpfs      371560      36    

    $ cat samples/df.txt | ./sel 1,2,3 where Used -gt 0, Filesystem -eq tmpfs
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used  
    tmpfs      371560      5956  
    tmpfs      1857800     56916 
    tmpfs      5120        4     
    tmpfs      371560      36    


"Dashless" filter grammar:

.. code::

    $ export SEL_GRAMMAR=2
    $ cat samples/df.txt | ./sel 1,2,3 where Used gt 0 and Filesystem eq tmpfs
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used  
    tmpfs      371560      5956  
    tmpfs      1857800     56916 
    tmpfs      5120        4     
    tmpfs      371560      36    


    $ cat samples/df.txt | ./sel 1,2,3 where Used gt 0, Filesystem eq tmpfs
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used  
    tmpfs      371560      5956  
    tmpfs      1857800     56916 
    tmpfs      5120        4     
    tmpfs      371560      36    


Case-sensitibility for columns and literals:

.. code::
 
    # cat samples/df.txt | ./sel 1,2,3 where used gt 0 and filesystem eq Tmpfs
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used 
    
    $ cat samples/df.txt | ./sel 1,2,3 where used gt 0 and Filesystem eq tmpfs                  
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used  
    tmpfs      371560      5956  
    tmpfs      1857800     56916 
    tmpfs      5120        4     
    tmpfs      371560      36    
    
    
    $ cat samples/df.txt | ./sel 1,2,3 where used gt 0 and filesystem eq TMPFS
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used 
    
    
    $ cat samples/df.txt | ./sel 1,2,3 where used gt 0,Filesystem eq tmpfs
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used 
    
    
    $ cat samples/df.txt | ./sel 1,2,3 where used gt 0,Filesystem eq tmpfs
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used  
    tmpfs      371560      5956  
    tmpfs      1857800     56916 
    tmpfs      5120        4     
    tmpfs      371560      36    
    
More complicated example

.. code::

    $ cat sample/df.txt | ./sel -f 1,2,Used,Used,Mounted -w Used -gt 0 -a Mounted -starts /rr
    # meta prog:/bin/df
    #
    Filesystem             1024-blocks Used      Used      Mounted   
    /dev/sdx2              123723748   10807024  10807024  /rr/rrr   
    1.2.3.4:/foobar2/test1 956688640   740075264 740075264 /rr/test1 
    1.2.3.4:/foobar2/test2 956688640   740075264 740075264 /rr/test2 
    1.2.3.4:/foobar2/test9 956688640   740075264 740075264 /rr/test9 

    
