GitMedia
========

Testing project to try large file support using git-media.
This readme is basically same with the git-media link below with my minor corrections.

https://github.com/schacon/git-media/blob/master/README.rdoc

Installation
------------
    $ sudo gem install trollop
    $ sudo gem install s3
    $ sudo gem install ruby-atmos-pure
    $ sudo gem install right_aws
    Clone gi-media
    $ gem build git-media.gemspec
    $ sudo gem install git-media-0.1.2.gem

Configuration
-------------
Go either in ~/.gitconfig (for global settings) or in clone/.git/config (for per-repo settings).

    $ GitMedia/.git [master]$ more config 
    [core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
    [remote "origin"]
        fetch = +refs/heads/*:refs/remotes/origin/*
        url = https://github.com/katiekuwata/GitMedia.git
    [branch "master"]
        remote = origin
        merge = refs/heads/master
    [filter "media"]
        clean = git-media filter-clean
        smudge = git-media filter-smudge
    [git-media]
        transport = local
        localpath = <Local Path>

One time

    $ echo “*.mov filter=media -crlf” > .gitattributes



