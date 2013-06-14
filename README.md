git-cache
=========

Git extension to cache upstream repos on a local server

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
