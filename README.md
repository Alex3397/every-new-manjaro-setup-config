# Every new manjaro setup and config.
Install and setup configuration for my own use, so that every time I boot a new manjaro distro seting up stuff go as fast and smooth as possible. It'll serve as way to store useful commands for downloading regularly used apps and their configurations.

### Installation.

```bash
$ sudo pacman -Sy postgresql pgadmin4 mariadb clementine code npm firefox intellij-idea-community-edition jre11-openjdk maven neofetch youtube-dl elasticsearch kibana logstash nginx
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
$ sudo systemctl start postgresql
$ sudo systemctl enable postgresql
$ sudo systemctl status postgresql
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

And all is done, run pgAdmin4 to set the server up `that's up to you, I know you got this`.

### Snap apps Instalation.


```bash
$ sudo snap install --classic heroku
$ sudo snap install postman
```

### Instaling python dependencies.

```bash
$ pip3 install pandas psycopg2 xlrd openpyxl xlsxwriter sqlalchemy
```

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
`Since my pc has 16Gb RAM install i'll assing 2Gbs`
 
 ```bash
-Xms2g
-Xmx2g
 ```
 
Depending on your hardware you may want to enable elasticsearch so it starts at boot.
`Since i'm not a RAM consuming freak I'll start elasticsearh kibana and logstash every time I need these tools.
If you're using a proper serve to host these processes then go ahead and enable start on boot`

```bash
$ sudo systemctl start elasticsearch.service
$ sudo systemctl enable elasticsearch.service
```

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

Uncomment these fields:
```bash
server.port: 5601
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

If every things ok. 
You're blessed by the gods of computing, a demigod walking among stardust.

For the rest of us, mere mortals, that doesn't run node v10.22.1.Keep on reading.

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

Edit ExecStart=/usr/bin/node field to:

```bash
ExecStart=/usr/bin/node10221
```

Restart systemctl services and check status.

```bash
$ sudo systemctl daemon-reload
$ sudo systemctl start kibana.service
$ sudo systemctl status kibana.service
```

If every thing's ok. Thank god.
Other wise you gotta find out what's causing the issue.
Tip: use `$ journalctl -fu kibana.service` and google.

After solving the issue go to `http://localhost:5601` to check it out.

Later you might need to set kibana `server.host` to `0.0.0.0` or another IP address you need.

That's all for today folks. More updates will come soon.
