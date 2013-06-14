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

Installation
------------

The solution comes with two scripts:
* 'git-cache-clone' to be installed on all client machines
* 'gitcd' to be installed on the server


Choose the machine and an account that will be used to store cached repositories.

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

