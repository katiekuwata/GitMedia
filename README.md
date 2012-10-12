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

    $ GitMedia [master]$ git config filter.media.smudge "git-media filter-smudge"
    $ GitMedia [master]$ git config filter.media.clean "git-media filter-clean"
    $ GitMedia [master]$ git config --global git-media.transport "local"
    $ GitMedia [master]$ git config --global git-media.localpath "/Documents/MyLocalRepoPath"

    $ GitMedia/.git [master]$ more config 
    <snip>
    [filter "media"]
        clean = git-media filter-clean
        smudge = git-media filter-smudge
    <snip>
   
    $ GitMedia/.git [master]$ more ~/.gitconfig 
    <snip>
    [git-media]
        transport = local
        localpath = /Documents/MyLocalRepoPath
    <snip>
   
Setup the .gitattributes file to map extensions to the filter. We are going to filter *.png file in this example. Do once

    $ GitMedia [master]$ echo "*.png filter=media -crlf" > .gitattributes

Try out
-------
Add robot.png and add/commit/push to github and upload media file to media repository

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
    
Clone the repository from github and download media files from the media repository

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
	
Since I setup .git/config locally I need to do this here too

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

Media files and commit
----------------------
    $ GitMedia [master]$ git media status
    == Expanded Media ==
       (417k)   lion.png
       (415k)   robot.png

    == Already Pushed Media ==
       (415k)   b9d4020b

    $ GitMedia [master]$ git status
    # On branch master
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #	modified:   lion.png
    #
    no changes added to commit (use "git add" and/or "git commit -a")

    $ GitMedia [master]$ git add .
    Saving media : e46cc216d91370377254cdb677c31554d6a64aca : 0.004112

    $ GitMedia [master]$ git commit -m "Added annotation #1"
    [master Saving media : e46cc216d91370377254cdb677c31554d6a64aca : 0.004207
    8572e93] Added annotation #1
    Saving media : e46cc216d91370377254cdb677c31554d6a64aca : 0.004179
     1 files changed, 1 insertions(+), 1 deletions(-)

    $ GitMedia [master]$ 2012-08-20 17:42:21.896 GitX[90387:c337] Loaded 9 commits in 0.008862 seconds (1015.569976/sec)

    $ GitMedia [master]$ git status
    # On branch master
    # Your branch is ahead of 'origin/master' by 1 commit.
    #
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #	modified:   lion.png
    #
    no changes added to commit (use "git add" and/or "git commit -a")

    $ GitMedia [master]$ git add .
    Saving media : d55a612d68258b93e4ccded1cd04c509a8cc0be1 : 0.004242

    $ GitMedia [master]$ git commit -m "Added annotation #2"
    [master Saving media : d55a612d68258b93e4ccded1cd04c509a8cc0be1 : 0.004149
    4bc115e] Added annotation #2
    Saving media : d55a612d68258b93e4ccded1cd04c509a8cc0be1 : 0.004289
     1 files changed, 1 insertions(+), 1 deletions(-)

    $ GitMedia [master]$ git status
    # On branch master
    # Your branch is ahead of 'origin/master' by 2 commits.
    #
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git checkout -- <file>..." to discard changes in working directory)
    #
    #	modified:   lion.png
    #
    no changes added to commit (use "git add" and/or "git commit -a")

    $ GitMedia [master]$ git add .
    Saving media : 9cdd83c1ba83761a289f64dcf555f4b7a9287df7 : 0.004066

    $ GitMedia [master]$ git commit -m "Added annotation #2"
    [master Saving media : 9cdd83c1ba83761a289f64dcf555f4b7a9287df7 : 0.004163
    6c61e57] Added annotation #2
    Saving media : 9cdd83c1ba83761a289f64dcf555f4b7a9287df7 : 0.004186
     1 files changed, 1 insertions(+), 1 deletions(-)

    $ GitMedia [master]$ git commit --amend
    Saving media : 9cdd83c1ba83761a289f64dcf555f4b7a9287df7 : 0.004151
    [master Saving media : 9cdd83c1ba83761a289f64dcf555f4b7a9287df7 : 0.004124
    8771bb8] Added annotation #3
    Saving media : 9cdd83c1ba83761a289f64dcf555f4b7a9287df7 : 0.004419
     1 files changed, 1 insertions(+), 1 deletions(-)

    $ GitMedia [master]$ ll ../GitMediaFiles/
    -rw-------   1   427384 Aug 20 12:06 f13fba2af641db2ccf987e31938b2c033017fbe7
    -rw-------   1   425535 Aug 20 12:25 b9d4020b63951a614f81c883b2fb9918892d5cfb

    $ GitMedia [master]$ git media sync
    uploading 9cdd83c1
    uploading d55a612d
    uploading e46cc216

    $ GitMedia [master]$ ll ../GitMediaFiles/
    -rw-------   1   427384 Aug 20 12:06 f13fba2af641db2ccf987e31938b2c033017fbe7
    -rw-------   1   425535 Aug 20 12:25 b9d4020b63951a614f81c883b2fb9918892d5cfb
    -rw-------   1   427579 Aug 20 17:45 e46cc216d91370377254cdb677c31554d6a64aca
    -rw-------   1   427678 Aug 20 17:45 d55a612d68258b93e4ccded1cd04c509a8cc0be1
    -rw-------   1   427713 Aug 20 17:45 9cdd83c1ba83761a289f64dcf555f4b7a9287df7

