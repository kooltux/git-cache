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
staying on external sites. Using git-cache reduces significantly the external bandwith usage due to repeated fetchs.

From the developer point of view, the cloned repository is usable transparently: pushes occur on the primary upstream server.

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

For example:
<pre>
$ git clone https://github.com/kooltux/git-cache.git
$ ln -s git-cache/git-cclone ~/bin/
$ ln -s git-cache/git-crecover ~/bin
</pre>

Create the file (or copy git-cache/gitcache.conf) **~/.gitcache.conf** and edit it to put the 
definition of the variable **GIT_CACHE_SERVER** as in the example below:

<pre>
GIT_CACHE_SERVER=XXXX
</pre>

where *XXXX* stands for **git@proxy** or **me@my.company.com** .

This is how a client can connect to the cache using ssh. Feel free to use ssh-copy-id to install public keys on the cache server:

<pre>
$ . ~/.gitcache.conf
$ ssh-copy-id $GIT_CACHE_SERVER
</pre>

Refer to installation of the cache server to get full understanding of what has
to be set.

**NOTE:** you can also define the variable **GIT_CACHE_SERVER** in your profile 
or rc files (.profile or .bashrc or .zshrc or ....)

Usage on the client
-------------------

After installation, the client mainly have the command to clone git repositories
through the cache. The standard clone is still available as usual.

To clone a directory use can use either the command 'git-cclone' or the command
'git cclone'.

Usage: **git cclone repository [directory [--mirror|--bare]]**

**Please note**: Currently, most of the options of 'git clone' aren't available through 'cclone'.

The effect of the command is:
- access the git cache server to ensure caching of the repository 
(it will produce a creation or a fetch on server side);
- clone the cached git;
- configure the cloned project to push on original git server and to fetch on cache.

Example:
<pre>
$ git cclone git@github.com:kooltux/git-cache.git git-cache
$ cd git-cache
$ git remote -v
origin	git@gitcache.localdomain:/srv/git/gitcache/github.com//kooltux/git-cache.git (fetch)
origin	https://github.com/kooltux/git-cache (push)
</pre>

The client can also use the command 'git-crecover' (that is also 'git crecover').
This command can either recover for a cache repository that was removed from the
cache server or it can merely make a cache entry for an existing git clone (in other words, 
it inserts a fetching cache for a repository that was cloned in a standard way **without
cache**).

'git-crecover' is interactive. Go to the directory of the cloned repository and call it
with 'git-crecover' or 'git crecover'. It will ask you what to do: REMOVE or RECOVER.

**REMOVE:** remove any reference to any caching of git.

**RECOVER:** insert the git cache references and ensure that the cached repository exists.


Interesting side effect: 'git crecover' can also transform a non-cached clone in a cached clone when using the **RECOVER** command.

Installation of the cache server
--------------------------------

Choose the machine and the account that will be used to store cached repositories
and run the server script.

Log on the machine, clone the repo and copy 'gitcache-server' in a directory referenced by $PATH

<pre>
$ git clone https://github.com/kooltux/git-cache.git

$ ln -s git-cache/gitcache-server ~/bin/

$ cp git-cache/gitcache-server.conf ~/.gitcache-server.conf
</pre>

Edit the ~/.gitcache-server.conf file and ajust the variables:
* GIT_CACHE_DIR : the cache directory where all git repos will be stored
* GIT_CACHE_SERVER_PRIMARY : (optional) primary cache server for chaining


Using cache standalone (without server)
---------------------------------------

Into your account, you can put the client AND the server too. The server will still be 
accessed with ssh. Here below the examples of configuring your **.profile** or **.bashrc**:

<pre>
GIT_CACHE_SERVER=$(id _un)@localhost
GIT_CACHE_DIR=~/.gitcachedir
</pre>

Chaining caches
---------------

Using the GIT_CACHE_SERVER_PRIMARY variable, it's possible to chain caches.

To use this, install git-cclone and git-crecover on the server too (add them to execution path).

When gitcache-server is invoked to cache a new repo, it will try to fetch it from another cache and so on.

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

FAQ
---

*_Q:_* And for Windows?

*_A:_* Hahahahahahahaha

*_Q:_* But seriously...

*_A:_* Well, fork and let now us the URL to put in place of the laugths.

