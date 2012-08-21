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
        localpath = /Documents/MyLocalRepoPath

One time

    $ echo “*.mov filter=media -crlf” > .gitattributes

Try out
-------
Add robot.png

    $ GitMedia [master]$ git status
    # On branch master
    # Untracked files:
    #   (use "git add <file>..." to include in what will be committed)
    #
    #	robot.png
    nothing added to commit but untracked files present (use "git add" to track)
    
    $ GitMedia [master]$ git media status
    == Expanded Media ==
       (417k)   lion.png

    $ GitMedia [master]$ git add robot.png
    Saving media : b9d4020b63951a614f81c883b2fb9918892d5cfb : 0.003978

    $ GitMedia [master]$ git commit -m "Added robot.png"
    [master 827c3b0] Added robot.png
    Saving media : b9d4020b63951a614f81c883b2fb9918892d5cfb : 0.004152
     1 files changed, 1 insertions(+), 0 deletions(-)
     create mode 100644 robot.png

    $ GitMedia [master]$ git media status
    == Expanded Media ==
       (417k)   lion.png
       (415k)   robot.png

    == Unpushed Media ==
       (415k)   b9d4020b

    $ GitMedia [master]$ git media sync
    uploading b9d4020b

    $ GitMedia [master]$ ll /Documents/MyLocalRepoPath
    -rw-------  1   427384 Aug 20 12:06 f13fba2af641db2ccf987e31938b2c033017fbe7
    -rw-------  1   425535 Aug 20 12:25 b9d4020b63951a614f81c883b2fb9918892d5cfb

    $ GitMedia [master]$ git push origin 
    
Clone the repo

clone from github

    $ git clone https://github.com/katiekuwata/GitMedia.git GitMediaClone

    $ GitMediaClone [master]$ ll
        -rw-rw-r--   1    41 Aug 20 12:28 robot.png
	    -rw-rw-r--   1    41 Aug 20 12:28 lion.png
	    -rw-rw-r--   1    53 Aug 20 12:28 README.md
	    -rw-rw-r--   1    11 Aug 20 12:28 .gitignore
	    -rw-rw-r--   1    25 Aug 20 12:28 .gitattributes
	    drwxrwxr-x  13   442 Aug 20 12:28 .git

    $ GitMediaClone [master]$ more robot.png  <--------- sha file
    b9d4020b63951a614f81c883b2fb9918892d5cfb
	
    $ GitMediaClone [master]$ more lion.png   <--------- sha file
    f13fba2af641db2ccf987e31938b2c033017fbe7
	
since I setup .git/config locally I need to do this here too

    GitMediaClone [master]$ git config filter.media.smudge "git-media filter-smudge"
    GitMediaClone [master]$ git config filter.media.clean "git-media filter-clean"
    GitMediaClone [master]$ git config git-media.transport "local"
    GitMediaClone [master]$ git config git-media.localpath "/Documents/MyLocalRepoPath"

    GitMediaClone [master]$ git media status
       == Unexpanded Media ==
       f13fba2a lion.png
       b9d4020b robot.png

    GitMediaClone [master]$ git media sync <----------- synced!
    	Downloading f13fba2a : lion.png
    	Expanding  f13fba2a : lion.png
    	Downloading b9d4020b : robot.png
    	Expanding  b9d4020b : robot.png

    GitMediaClone [master]$ ll
    	total 1696
    	-rw-rw-r--   1      53 Aug 20 12:28 README.md
    	-rw-rw-r--   1      11 Aug 20 12:28 .gitignore
    	-rw-rw-r--   1      25 Aug 20 12:28 .gitattributes
    	drwxrwxr-x  14     476 Aug 20 12:37 .git
    	-rw-rw-r--   1  425535 Aug 20 12:38 robot.png <---------- real file
    	-rw-rw-r--   1  427384 Aug 20 12:38 lion.png <---------- real file

