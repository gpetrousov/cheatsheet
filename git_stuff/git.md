### Documentation and how to ###

Some nice docs I found:

- http://www-cs-students.stanford.edu/~blynn/gitmagic/ch01.html
- http://www.git-tower.com/learn/ebook/command-line/introduction

## Some unchecked guides from archlinux ##
    http://www.trueelena.org/computers/howto/git_server_with_gitolite_gitweb_and_lighttpd.html
    http://sysphere.org/~anrxc/j/archives/2009/09/17/notes_on_lighttpd_and_git/

<br />

### GIT PUBLIC ON APACHE ###

- guide
		
		https://www.kernel.org/pub/software/scm/git/docs/howto/setup-git-server-over-http.html

- install

		apt-get install apache2

- start

		/etc/init.d/apache2 restart
		apachectl graceful #dont lose active connections
		apachectl graceful-stop #stop apache
		cd /var/www
		mkdir my-new-repo.git

- Initialize a bare repository
		
		cd my-new-repo.git
		git --bare init
		chmod -R a-w #chmod -R 555 .

- Restart apache2, and check http://server/my-new-repo.git

<br />
## GIT PUBLIC ON lighttpd and gitweb ##
	http://box.matto.nl/gitwebwheezy.html

