# Every new manjaro setup and config.
Install and setup configuration for my own use, so that every time I install a new manjaro distro setting up stuff go as fast and smooth as possible.

### Installation.

```bash$``````bash
sudo pacman -Sy python geckodriver tk postgresql pgadmin4 apache mysql mariadb lollypop code npm firefox intellij-idea-community-edition jre17-openjdk maven neofetch youtube-dl nginx virt-manager qemu vde2 ebtables dnsmasq bridge-utils openbsd-netcat wine lutris steam-manjaro gsmartcontrol smartmontools xterm git snapd tmux ncdu bashtop ranger github-cli
```
```bash
elasticsearch kibana logstash are deprecated
```

### Login with Github CLI

```bash
$ gh auth login
```

### Snap apps Instalation.

```bash
$ sudo systemctl enable --now snapd.socket
```

```bash
$ sudo ln -s /var/lib/snapd/snap /snap
```

```bash
$ sudo snap install --classic heroku && sudo snap install postman && sudo snap install android-studio --classic
```

### Instaling python dependencies.

```bash
$ pip3 install selenium python-decouple pyautogui pandas psycopg2 xlrd openpyxl xlsxwriter sqlalchemy python-decouple googleapis-common-protos google-auth-oauthlib google-auth-httplib2 google-ads google-api-core google-api-python-client google-auth simple-websocket
```

### Add Permanent Enviroment Variables.

Touch .sh file then open and edit:

```bash
$ sudo touch /etc/profile.d/example.sh
$ sudo nano /etc/profile.d/example.sh
```

Template:

```bash
export LOCAL_BASE_URL=jdbc:postgresql://localhost:5432/postgres
export LOCAL_DATABASE_PASS=postgres
export LOCAL_DATABASE_USER=postgres
```

Or 

```bash
$ sudo nano /etc/environment
```

and add:

```bash
LOCAL_BASE_URL=jdbc:postgresql://localhost:5432/postgres
LOCAL_DATABASE_PASS=postgres
LOCAL_DATABASE_USER=postgres
```

Database basic URL template:

```bash
jdbc:postgresql://localhost:5432/database
```

### PostgreSQL Service setup.

Sign up as postgres user, initiate database using system locale language, and nothing else.

```bash
$ sudo su postgres -l
$ initdb --locale $LANG -E UTF8 -D '/var/lib/postgres/data/'
$ exit
```

Start PostgreSQL service, enable it to start at every boot and check if nothing is wrong.

```bash
$ sudo systemctl start postgresql && sudo systemctl enable postgresql && sudo systemctl status postgresql
```

If the following shows up it's ready to go. Otherwise, go figure out what's wrong.

```bash
● postgresql.service - PostgreSQL database server
     Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
     Active: active (running) since Mon 2021-01-11 11:49:24 -03; 1h 5min ago
    Process: 459 ExecStartPre=/usr/bin/postgresql-check-db-dir ${PGROOT}/data (code=exited, status=0/SUCCESS)
   Main PID: 507 (postgres)
      Tasks: 7 (limit: 19129)
     Memory: 25.6M
     CGroup: /system.slice/postgresql.service
             ├─507 /usr/bin/postgres -D /var/lib/postgres/data
             ├─555 postgres: checkpointer
             ├─556 postgres: background writer
             ├─557 postgres: walwriter
             ├─558 postgres: autovacuum launcher
             ├─559 postgres: stats collector
             └─560 postgres: logical replication launcher

jan 11 11:49:23 alexandre-systemproductname systemd[1]: Starting PostgreSQL database server...
jan 11 11:49:24 alexandre-systemproductname postgres[507]: 2021-01-11 11:49:24.196 -03 [507] LOG:  starting PostgreSQL 13.1 on x86_64-pc-linux-gnu, compiled by gcc (GCC>
jan 11 11:49:24 alexandre-systemproductname postgres[507]: 2021-01-11 11:49:24.198 -03 [507] LOG:  listening on IPv6 address "::1", port 5432
jan 11 11:49:24 alexandre-systemproductname postgres[507]: 2021-01-11 11:49:24.198 -03 [507] LOG:  listening on IPv4 address "127.0.0.1", port 5432
jan 11 11:49:24 alexandre-systemproductname postgres[507]: 2021-01-11 11:49:24.201 -03 [507] LOG:  listening on Unix socket "/run/postgresql/.s.PGSQL.5432"
jan 11 11:49:24 alexandre-systemproductname postgres[547]: 2021-01-11 11:49:24.215 -03 [547] LOG:  database system was shut down at 2021-01-11 04:25:40 -03
jan 11 11:49:24 alexandre-systemproductname postgres[507]: 2021-01-11 11:49:24.237 -03 [507] LOG:  database system is ready to accept connections
jan 11 11:49:24 alexandre-systemproductname systemd[1]: Started PostgreSQL database server.
```

### Postgres User setup.

Sign up as postgres user, enter PostgreSQL via terminal and ALTER USER password.

```bash
$ sudo -iu postgres
$ psql
postgres=# ALTER USER postgres WITH PASSWORD 'mypassword';
postgres=#\q
$ exit
```

And all is done, run pgAdmin4 to set the server up.

### If PgAdmin4 fails

Install pgAdmin4 via pip, if it doesn't install via `.whl`.

```bash
$ sudo mkdir /var/lib/pgadmin && sudo mkdir /var/log/pgadmin
$ sudo chown $USER /var/lib/pgadmin && sudo chown $USER /var/log/pgadmin
$ pip3 install pgadmin4
```

And run `$ pgadmin4` on terminal to finish setting up and then create a desktop shortcut.

### Elasticsearch configuration

Enable cross origin access.

```bash
$ sudo su
$ echo 'http.cors.allow-origin: "/.*/"' >> /etc/elasticsearch/elasticsearch.yml 
$ echo 'http.cors.enabled: true' >> /etc/elasticsearch/elasticsearch.yml
```

Making acessible on local network.

`Still in super user mode`
```bash
$ echo 'network.bind_host: 0.0.0.0' >> /etc/elasticsearch/elasticsearch.yml
$ echo 'node.master: true' >> /etc/elasticsearch/elasticsearch.yml
$ echo 'node.data: true' >> /etc/elasticsearch/elasticsearch.yml
$ echo 'transport.host: localhost' >> /etc/elasticsearch/elasticsearch.yml
$ echo 'transport.tcp.port: 9300' >> /etc/elasticsearch/elasticsearch.yml
$ exit
```

### Alocating JVM memory.
`Procede with caution.`

```bash
nano /etc/elasticsearch/jvm.options
```
You need to edit the -Xms and -Xmx values, please consider how much RAM you hardware's got this may either crash your system or cause weird behavior.
Note: `-Xms set's up the minimum heap size while -Xmx set's up the maximum heap size. And according to elasticsearch's heap size documentation BOTH -Xms AND -Xmx to no more than 50% percent your system's physical RAM as it requires memory for more than just JVM heap. However I've taken upon myself to test how much RAM is needed to properly run elasticsearch alongside with kibana, logstash and nginx and find out what goes wrong when -Xms and -Xmx are different from oneanother. For now I'll start low:`
 
 ```bash
-Xms1g
-Xmx1g
 ```
 
Depending on your hardware you may want to enable elasticsearch so it starts at boot.

```bash
$ sudo systemctl start elasticsearch.service
$ sudo systemctl enable elasticsearch.service
```
`Since I don't have server sitting in mmy desktop I'll only start elasticsearh kibana, logstash and nginx every time I need these tools.
If you're using a proper serve to host these processes then go ahead and enable start on boot`

If an error occurs run:

```bash
$ journalctl -fu elastic.service
```

To get an insight on what's wrong, if there's nothing useful in it ask doctor google.

### Configuring Kibana.

Edit the main Kibana configuration file is located at `/etc/kibana/kibana.yml`. By default kibana does not specify a port or bind address. 

```bash
$ sudo nano /etc/kibana/kibana.yml
```

Uncomment these fields and set kibana `server.host` to `"0.0.0.0"` or another IP address you need.
```bash
server.port: "0.0.0.0"
server.host: "localhost"
elasticsearch.hosts: ["http://localhost:9200"]
```

Start kibana.

```bash
$ sudo systemctl start kibana.service
```

Wait 3 seconds.

Get kibana status.

```bash
$ systemctl status kibana.service
```

If every thing is ok it means they've fixed it already. 

## What's the issue.

Right now Kibana runs on node v10.22.1 and probably lesser versions `I haven't tested that nor I will`, until Kibana starts using node's newer versions this issue is going to repeat itself.

## The solution.

### Install nvm.

```bash
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.37.0/install.sh | bash
$ export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
$ [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

I know it looks weird, but it works.

### Install node v10.22.1 using nvm.

```bash
$ nvm install v10.22.1
```

This installs node bin in `/home/${your_user}/.nvm/versions/node/v10.22.1/bin` .nvm hidden directory.

### Move into `/usr/bin` and copy node v10.22.1 into it named differently.

```bash
$ sudo cp -- /home/${your_user}/.nvm/version/node/v10.22.1/bin/node node10221
```

### Setup kibana to initialized using node's older version.

```bash
$ sudo nano /usr/lib/systemd/system/kibana.service
```

Edit `ExecStart=/usr/bin/node` field to:

```bash
ExecStart=/usr/bin/node10221
```

Restart systemctl services and check status.

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl start kibana.service
$ sudo systemctl status kibana.service
```

If every thing's ok, move on.

Other wise use `$ journalctl -fu kibana.service` and google.

### Configuring Logstash.

Edit logstash config to allow logs in to the machine.

```bash
$ sudo nano /etc/logstash/conf.d/logstash-simple.conf
```

Add this:

```bash
input {
  file {
    path => "/var/log/faillog" 
    start_position => beginning
  }

  # network syslog input
  syslog {
    host => "0.0.0.0" 
    port => 514
  }

  beats {
    port => 5044
  }

}

output {
  elasticsearch { host => localhost }
}
```

Test service start.

```bash
$ systemctl start logstash.service
```

If start fails, this might be the issue:

A fresh logstash install, at the moment I'm writing this, standart pacman install is broken. In order to solve this problem we once again have to edit the ELK.service files.

Opening `logstash.service` file.

```bash
$ sudo nano /usr/lib/systemd/system/logstash.service
```

The original service file is like this:

```bash
[Unit]
Description=Logstash
Documentation=http://www.elastic.co
After=elasticsearch.service

[Service]
Environment=LS_HOME=/var/lib/logstash
Environment=LS_HEAP_SIZE="500m"
Environment=LS_CONF_DIR=/etc/logstash/conf.d
Environment=LS_LOG_DIR=/var/log/logstash
Environment=LS_SETTINGS_DIR=/etc/logstash
User=logstash
Group=logstash
ExecStart=/usr/share/logstash/bin/logstash -f $LS_CONF_DIR  --path.logs $LS_LOG_DIR --path.data $LS_HOME --path.settings $LS_SETTINGS_DIR
StandardOutput=null
StandardError=journal
SuccessExitStatus=143
LimitNOFILE=65535
TimeoutStopSec=20
LimitMEMLOCK=infinity

[Install]
WantedBy=multi-user.target
```

Take a look, try to identify the problem by yourself for a moment. If you can, great, if not it's alright, it's quite a simple and weird error, realy.

The error was in field:

```bash
ExecStart=/usr/share/logstash/bin/logstash -f $LS_CONF_DIR  --path.logs $LS_LOG_DIR --path.data $LS_HOME --path.settings $LS_SETTINGS_DIR
```

That is basically bash variable definition, it's used alot, and technically it is not wrong, that's why it's a weird issue, because when adding `{` and `}` to each variable it starts working. So set it like this:

```bash
ExecStart=/usr/share/logstash/bin/logstash -f ${LS_CONF_DIR}  --path.logs ${LS_LOG_DIR} --path.data ${LS_HOME} --path.settings ${LS_SETTINGS_DIR}
```

Alternatively it can bet set to:

```bash
ExecStart=/usr/share/logstash/bin/logstash -f /etc/logstash/conf.d  --path.logs /var/log/logstash --path.data /var/lib/logstash --path.settings /etc/logstash
```

It should does not matter which way you choose.

After that, reload daemon and start logstash and get status.

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl start logstash.service
$ sudo systemctl status logstash.service
```

If no error occurs, that's all you need to do.

### Configuring ngnix

Open nginx config file.

```bash
$ sudo nano /etc/nginx/nginx.conf
```

Original file is like this:

```bash
#user html;
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
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
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
    #}


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
```

If you got a fresh install feel free to just erase everything in the config file and make it like this:

```bash
#user html;
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
    types_hash_max_size 4096;
    server_names_hash_bucket_size 128;
    
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
        listen                80;
        server_name           localhost;
        access_log            /var/log/nginx-logstash.log;

        auth_basic "Restricted Access";
        auth_basic_user_file /etc/kibana/htpasswd.users;

        location / {
            proxy_pass http://localhost:5601;
            proxy_http_version 1.1;
            proxy_set_header Upgrade ${http_upgrade};
            proxy_set_header Connection 'upgrade';
            proxy_set_header Host ${host};
            proxy_cache_bypass ${http_upgrade};        
        }
    }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        #error_page   500 502 503 504  /50x.html;
        #location = /50x.html {
        #    root   /usr/share/nginx/html;
        #}

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
    #}


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
```

Generate htpassword for basic reverse proxy authentication.

```bash
$ sudo htpasswd -c -b /etc/kibana/htpasswd.users username password
```

And all is done, start nginx and check status just to make sure everything is working fine. If you get any errors it is most likely to be due to incorrect configuration data in `/etc/nginx/nginx.conf`.

```bash
$ sudo systemctl start nginx.service
$ sudo systemctl status nginx.service
```

##MySQL Configuration

MySQL might not work straight away so first thing you should do is check service status with:

```bash
$ sudo systemctl status mariadb.service
```

if it fails try:

```bash
$ sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
```

if results shows ok proceed with the installation:

```bash
$ sudo systemctl start mysqld && sudo mysql_secure_installation
```
