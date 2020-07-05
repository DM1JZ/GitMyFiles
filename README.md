# GitMyFiles

gitfs didn't work for me, so I wrote a quick and dirty workaround using inotify and git. Not exactly what gitfs does, but sufficient for my purposes.
This tracks file operations in a given git repo and executes the git add and commit commands for the corresponding files changes automatically. The commit message is the output of inotify, i.e. the event that ocurred and the path of the affected files.

## Install & Config

* install inotify-tools

* init git repo in dir to be observed (if not already done) (use .gitignore to exclude e.g. .swp files)

* create `/opt/gitsync.sh` as root:

```shell
		#!/bin/bash
		DIR=/home/user/pathto/mygitrepo
		LOGFILE=/opt/gitsynclog.log
		# echo $1 >> LOGFILE # enable for debug purposes
		cd $DIR && git add -A  # 2>&1 >> LOGFILE # enable for debug purposes
		git commit -m "$1" # 2>&1 >> LOGFILE # enable for debug purposes
		git push
```

* `chmod +x /opt/gitsync.sh`

* sudo incrontab -e:

	```shell
		/home/user/pathto/mygitrepo/	IN_MODIFY,IN_CREATE,IN_DELETE,IN_MOVE	  /opt/gitsync.sh '$% $@/$#'
	```

Make sure you have ssh access to the remote git and your passphrase is unlocked.

While testing, `incrond.service` crashed occasionally for some reasons (when creating a git repo while dir is already watched??). Maybe config `incrond.service` to restart automatically.
