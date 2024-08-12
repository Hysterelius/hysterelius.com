+++
title = "Setting up Nginx for Fedora Web Dev"
date = 2024-07-12
draft = false

[taxonomies]
categories = ["Linux"]
tags = ["meta", "linux"]

[extra]
toc = true
keywords = "fedora nginx web development linux setup nginx.conf configuration"
+++

To help me test my local website, I use a nginx setup so I can test everything. With HTTPS, automatic file serving and lightning fast development - no build step needed (kinda).

<!-- more -->

I am using Fedora, so I will be using `dnf` to install the packages.
The instructions will be similar if you are using a different distro, just replace `dnf` with your package manager.

## Install nginx

```bash
sudo dnf install nginx
```

This installs a web server - nginx, and all the necessary files.

We will use these files to configure our server.

## Configure nginx
We need to go and find the files to go and edit, so we can begin to run our web server!

Navigate to `/etc/nginx/` and you will see a few files and directories.

The main file to edit is the `nginx.conf` file, here we will place all our goodies to make sure the server will run well.


Edit the `nginx.conf` file, with your favourite text editor.
```bash
sudo nano /etc/nginx/nginx.conf
```

The file should look something like this:
```conf
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  localhost;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }
    # ~~snip~~ a bunch of server setup
}
```

And this is what my `nginx.conf` file looks like:
```conf,hl_lines=35
# For more information on configuration, see:
#   * Official English Documentation: http://nginx.org/en/docs/
#   * Official Russian Documentation: http://nginx.org/ru/docs/

user nginx;
worker_processes auto;
error_log /var/log/nginx/error.log notice;
pid /run/nginx.pid;

# Load dynamic modules. See /usr/share/doc/nginx/README.dynamic.
include /usr/share/nginx/modules/*.conf;

events {
    worker_connections 1024;
}

http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile            on;
    tcp_nopush          on;
    keepalive_timeout   65;
    types_hash_max_size 4096;

    include             /etc/nginx/mime.types;
    default_type        application/octet-stream;

    # Load modular configuration files from the /etc/nginx/conf.d directory.
    # See http://nginx.org/en/docs/ngx_core_module.html#include
    # for more information.
    include /etc/nginx/conf.d/*.conf;

}
```
This basically includes a singular change, it adds in the `include /etc/nginx/conf.d/*.conf;` line, which allows us to add in our own configuration files in one easy directory - so this file doesn't get too cluttered.

## Create a new configuration file
Lets say we are making a very simple web server, that serves a single file, `index.html`.

Now we can create a new configuration file in the `/etc/nginx/conf.d/` directory.
```bash
sudo nano /etc/nginx/conf.d/fedora-nginx-dev.conf
```

In this file, we can just add the following:
```conf
server {
    listen 80;
    server_name test.local;

    root /home/username/path/to/website;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    error_page 404 /404.html;
}
```
This serves the files at `/home/username/path/to/website` on the domain `test.local`.

#### Finding your website path
To find the path to your website, navigate to the directory where your website is stored, and run the following command:
```bash
readlink -f index.html
```

Which for me outputs:
```bash
readlink -f index.html
/home/ewing/.dev/blog/test-site/index.html
```

### Allowing your website to be served at `test.local`
To allow your website to be served at `test.local`, we need to add an entry to the `/etc/hosts` file to make sure your computer recognises this new "domain".

```bash
sudo nano /etc/hosts
```

Add the following line to the file:
```conf,hl_lines=8
# Loopback entries; do not change.
# For historical reasons, localhost precedes localhost.localdomain:
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
# See hosts(5) for proper format and other examples:
# 192.168.1.10 foo.example.org foo
# 192.168.1.13 bar.example.org bar
127.0.0.1 test.local
```

## Start the nginx server
Now we can start the nginx server, and see our website in action!

```bash
sudo systemctl start nginx
```

If you want to make sure the server starts on boot, you can run the following command:
```bash
sudo systemctl enable nginx
```

When I ran my program I had a few issues:

## Wrong Permissions
Fedora uses **SELinux** to help protect it from viruses and trojans, but it is also protecting us from our website!

We also need to confirm folder permissions as well.

Firstly, lets check what the `nginx` user is called.
```bash
cat /etc/passwd | grep nginx
```

Which printed this
```txt
nginx:x:970:968:Nginx web server:/var/lib/nginx:/sbin/nologin
```
And bingo! The nginx service is surprisingly called `nginx` 😯

#### Folder Permissions
Giving `nginx` the permissions for the folder:[^1]

[^1]: <https://serverfault.com/questions/947301/nginx-displaying-failed-13-permission-denied-when-trying-to-access-new-site>
```bash
sudo -u nginx namei /home/username/path/to/website
```

Which for me printed something like this:
```bash
f: /home/ewing/.dev/blog/test-site/index.html
d   /
d   home
d   ewing
    .dev - Permission denied
```
Which can be fixed for me with:
```bash
sudo setfacl -m g:nginx:rx /home/username
```

#### Too secure
To stop **SELinux** from trying to block our website, we need to tell Fedora that nginx is ok.

So we create a policy of all the violations, that will allow all these nginx to operate properly.

```bash
sudo ausearch -c 'nginx' --raw | audit2allow -M nginx-policy
sudo semodule -r nginx-policy
sudo systemctl daemon-reload
```

This should all nginx to operate all well.

## It should work!
Now if you go to your web browser of choice, you should see the `index.html` file that you pointed nginx at <http://test.local/>.

### Extra: adding HTTPS support
I added HTTPS support to my website, just so it works nice with my browsers.

Firstly install [mkcert](https://github.com/FiloSottile/mkcert) to add this support, which I had to install from [GitHub](https://github.com/FiloSottile/mkcert/releases/tag/v1.4.4).

```bash
curl -sL https://github.com/FiloSottile/mkcert/releases/download/v1.4.4/mkcert-v1.4.4-linux-amd64 -o mkcert
sudo chmod +x mkcert
sudo mv mkcert /usr/local/bin/mkcert
```

Now we need to make sure our system now recognises these new certificates we need to add _mkcert_ as a [CA](https://developer.mozilla.org/en-US/docs/Glossary/Certificate_authority), make sure to run this next command this **sudo** - as I found it leads to a few problems if it is not run with sudo.
```bash
sudo mkcert -install
```

Now go to back to `/etc/nginx/conf.d/`, so we can create a certs, make a new directory with `sudo mkdir certs`.

Then run these commands:
```bash
cd certs
sudo mkcert test.local "*.test.local" localhost 127.0.0.1 ::1
```

This will make two files:
- `test.local+4.pem`
- `test.local+4-key.pem`

We will then use these files in our new configuration:
Go back to `/etc/nginx/conf.d/`

And edit your `fedora-nginx-dev.conf` file to look now like this:
```conf,hl_lines=6
server {
    listen 80;
    server_name test.local;

    location / {
        return 301 https://$host$request_uri;
    }
}

server {
    listen 443 ssl;
    server_name test.local;

    ssl_certificate /etc/nginx/conf.d/test.local+4.pem;
    ssl_certificate_key /etc/nginx/conf.d/test.local+4-key.pem;

    root /home/username/path/to/website;
    index index.html index.htm;
    error_page 404 /404.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

With **301** redirecting all traffic to the new HTTPS domain.

## The end
Now if actually have a `index.html` at `/home/username/path/to/website`, you should see it served up in your favourite web browser at <https://test.local/>