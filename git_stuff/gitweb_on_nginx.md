Setup gitweb + nginx on Debian.

apt-get install nginx

apt-get install fcgiwrap spawn-fcgi

apt-get install gitweb

cat << EOF >>  /etc/nginx/sites-available/gitweb
server {
listen 80 default;

location /index.cgi {
root /usr/share/gitweb/;
include fastcgi_params;
gzip off;
fastcgi_param SCRIPT_NAME $uri;
fastcgi_param GITWEB_CONFIG /etc/gitweb.conf;
fastcgi_pass  unix:/var/run/fcgiwrap.socket;
}
location / {
root /usr/share/gitweb/;
index index.cgi;
}
}
EOF

ln -s /etc/nginx/sites-available/gitweb /etc/nginx/sites-enabled/gitweb
/etc/init.d/nginx restart

edit:
/usr/share/gitweb/index.cgi and /usr/share/gitweb/gitweb.cg
# name of your site or organization to appear in page titles
# replace this with something more descriptive for clearer bookmarks
our $site_name = ""
                 || ($ENV{'SERVER_NAME'} || "Untitled") . " Git";
