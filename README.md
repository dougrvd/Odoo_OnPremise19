# Installing Odoo 19.0 with one command (Supports multiple Odoo instances on one server).

> Based on [minhng92/odoo-19-docker-compose](https://github.com/minhng92/odoo-19-docker-compose) (MIT, © 2025 Minh Nguyen).
> Maintained here by Delta Aplicación with our own defaults; see [LICENSE](LICENSE).

## Quick Installation

Install [docker](https://docs.docker.com/get-docker/) and [docker-compose](https://docs.docker.com/compose/install/) yourself, then run the following to set up first Odoo instance @ `localhost:10019`. The installer generates a random master password and a random database password, and prints both when it finishes:

``` bash
curl -s https://raw.githubusercontent.com/dougrvd/Odoo_OnPremise19/main/run.sh | bash -s -- --destination odoo-one --port 10019 --chat 20019
```
and/or run the following to set up another Odoo instance @ `localhost:11019`:

``` bash
curl -s https://raw.githubusercontent.com/dougrvd/Odoo_OnPremise19/main/run.sh | bash -s -- --destination odoo-two --port 11019 --chat 21019
```

Arguments:
* `--destination` (**odoo-one**): Odoo deploy folder
* `--port` (**10019**): Odoo port
* `--chat` (**20019**): live chat port
* `--password` (optional): master password. If omitted, a random one is generated.
* `--db-password` (optional): PostgreSQL password. If omitted, a random one is generated.

If `curl` is not found, install it:

``` bash
$ sudo apt-get install curl
# or
$ sudo yum install curl
```

<p>
<img src="screenshots/odoo-19-docker-compose.gif" width="100%">
</p>

## Usage

Start the container:
``` sh
docker-compose up
```
Then open `localhost:10019` to access Odoo 19.

- **If you get any permission issues**, change the folder permission to make sure that the container is able to access the directory:

``` sh
$ sudo chmod -R 777 addons
$ sudo chmod -R 777 etc
$ sudo chmod -R 777 postgresql
```

- If you want to start the server with a different port, change **10019** to another value in **docker-compose.yml** inside the parent dir:

```
ports:
 - "10019:8069"
```

- To run Odoo container in detached mode (be able to close terminal without stopping Odoo):

```
docker-compose up -d
```

- To Use a restart policy, i.e. configure the restart policy for a container, change the value related to **restart** key in **docker-compose.yml** file to one of the following:
   - `no` =	Do not automatically restart the container. (the default)
   - `on-failure[:max-retries]` =	Restart the container if it exits due to an error, which manifests as a non-zero exit code. Optionally, limit the number of times the Docker daemon attempts to restart the container using the :max-retries option.
  - `always` =	Always restart the container if it stops. If it is manually stopped, it is restarted only when Docker daemon restarts or the container itself is manually restarted. (See the second bullet listed in restart policy details)
  - `unless-stopped`	= Similar to always, except that when the container is stopped (manually or otherwise), it is not restarted even after Docker daemon restarts.
```
 restart: always             # run as a service
```

- To increase maximum number of files watching from 8192 (default) to **524288**. In order to avoid error when we run multiple Odoo instances. This is an *optional step*. These commands are for Ubuntu user:

```
$ if grep -qF "fs.inotify.max_user_watches" /etc/sysctl.conf; then echo $(grep -F "fs.inotify.max_user_watches" /etc/sysctl.conf); else echo "fs.inotify.max_user_watches = 524288" | sudo tee -a /etc/sysctl.conf; fi
$ sudo sysctl -p    # apply new config immediately
``` 

## Custom addons

The **addons/** folder contains custom addons. Just put your custom addons if you have any.

## Odoo configuration & log

* To change Odoo configuration, edit file: **etc/odoo.conf**.
* Log file: **etc/odoo-server.log**
* The master password (**admin_passwd**) shipped in [etc/odoo.conf#L75](/etc/odoo.conf#L75) is the placeholder `CAMBIAR_ESTA_CLAVE`, not a usable password. `run.sh` replaces it with a random one at install time; if you deploy by hand with `docker compose`, set it yourself before exposing the instance — that password allows creating, dropping and restoring databases.

## Odoo container management

**Run Odoo**:

``` bash
docker-compose up -d
```

**Restart Odoo**:

``` bash
docker-compose restart
```

**Stop Odoo**:

``` bash
docker-compose down
```

## Live chat

In [docker-compose.yml#L20](docker-compose.yml#L20), we exposed port **20019** for live-chat on host.

Configuring **nginx** to activate live chat feature (in production):

``` conf
#...
server {
    #...
    location /longpolling/ {
        proxy_pass http://0.0.0.0:20019/longpolling/;
    }
    #...
}
#...
```

## docker-compose.yml

* odoo:19
* postgres:18

## Odoo 19.0 screenshots after successful installation.

<p align="center">
<img src="screenshots/odoo-19-welcome-screenshot.jpg" width="50%">
</p>

<p>
<img src="screenshots/odoo-19-apps-screenshot.jpg" width="100%">
</p>

<p>
<img src="screenshots/odoo-19-dashboard.jpg" width="100%">
</p>

<p>
<img src="screenshots/odoo-19-sales-screen.jpg" width="100%">
</p>

<p>
<img src="screenshots/odoo-19-product-form.jpg" width="100%">
</p>

