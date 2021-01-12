# Every new manjaro setup and config.
Install and setup configuration for my own use, so that every time I boot a new manjaro distro seting up stuff go as fast and smooth as possible. It'll serve as way to store useful commands for downloading regularly used apps and their configurations.

### Installation

```bash
$ sudo pacman -Sy postgresql pgadmin4 mariadb clementine code npm firefox intellij-idea-community-edition jre11-openjdk maven neofetch youtube-dl
```

### PostgreSQL Service setup

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

### Postgres User setup

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

### Instaling python dependencies

```bash
$ pip3 install pandas psycopg2 xlrd openpyxl xlsxwriter sqlalchemy
```

That's all for today folks. More updates will come soon.
