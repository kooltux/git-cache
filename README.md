git-cache
=========

Git extension to cache upstream repos on a local server

<pre>
       Figure 1: basic principle
       -------------------------

          +---------------+
          |  workstation  |
          +---v-------^---+
              |       |
              |       |fetch
              |       |
              |   +---^-------+
          push|   | git-cache |
              |   +---^-------+
              |       |
              |       |fetch
              |       |
          +---v-------^---+
          |  git server   |
          +---------------+
</pre>

Audience
--------

These scripts are for teams working on some git repositories
staying on external sites. Using it allow to reduce the external 
bandwidth used by git fetchs.

Content
-------

The solution comes with two scripts:
* 'git-cclone' and 'git-crecover' to be installed on all clients
* 'gitccache-server' to be installed on the cache server

And 2 examples of configuration files.
* 'gitcache.conf' for clients
* 'gitcache-server.conf' for servers

Installing the client scripts
-----------------------------

As user, put the scripts 'git-cclone' and 'git-crecover' in your
PATH.

Create the file **~/.gitcache.conf** and edit it to put the 
definition of the variable **GIT_CACHE_SERVER** as in the example below:

<pre>
GIT_CACHE_SERVER=XXXX
</pre>

where *XXXX* stands for **git@proxy** or **me@my.company.com**.

Refer to installation of the cache server to get full understanding of what have
to be set.

Usage on the client
-------------------

After installation, the client mainly have the command to clone git repositories
through the cache. The standard clone is still available as usual.

To clone a directory use can use either the command 'git-cclone' or the command
'git cclone'.

Usage: **git cclone repository [directory [--mirror|--bare]]**

Currently, most of the options of 'git clone' aren't available through 'cclone'.

The effect of the command is:
- access the git cache server to ensure caching of the repository 
(it will produce a creation or a fetching);
- clone the cached git;
- configure the cloned project to push on original git server and to fetch on cache.

The client can also use the command 'git-crecover' (that is also 'git crecover').



Installation of the cache server
--------------------------------


Choose the machine and the account on it that will be used to store cached repositories
and run the server script.


Log on the machine, clone the repo and link 'gitcd' in a directory referenced in $PATH

<pre>
$ ssh user@hostname 

$ git clone https://github.com/kooltux/git-cache.git

$ ln -s git-cache/bin/gitcd ~/bin/
</pre>

Log on a client machine, then again, install git-cache

<pre>
$ git clone https://github.com/kooltux/git-cache.git

$ ln -s git-cache/bin/git-cache-clone ~/bin/
$ ln -s git-cache/bin/git-cache-recover ~/bin/
</pre>


Usage
-----

Configuration
-------------

<pre>
   Figure 2: using a hierarchy of cache servers
   --------------------------------------------
   
    ......................................
    . high bit rate areas                .
    .                                    ...  
    .    +---------------+               . .  
    .    | +---------------+             . ...
    .    +-| +---------------+           . . .
    .      +-|  workstations |           . . .
    .        +---v-------^---+           . . .
    .            |       |               . . .
    .            |       |fetch          . . .
    .            |       |               . . .
    .            |   +---^-------+       . . .
    .        push|   | git-cache |       . . .
    .            |   +---^-------+       . . .
    .            |       |               . . . 
    .............|.......|................ . .
      .          |       |                 . .
      ...........|.v.....|.^................ .
        .        | |     | |                 .
        .........|.|.v...|.|.^................
                 | | |   | | |
                 | | |   | | |
                 | | | +-^-^-^-----+
                 | | | | git-cache |
           pushs | | | +-^---------+
                 | | |   |
                 | | |   | fetch
                 | | |   |
                 | | |   |   medium bitrate area
    .............|.|.|...|.....................
                 | | |   |   slow bitrate area
                 | | |   |
               +-v-v-v---^--+
               | git server |
               +------------+
   
</pre>

Security
--------

As for any client of SSH, you should be aware of the security holes of ssh.
Please refer to a good lesson about it.
