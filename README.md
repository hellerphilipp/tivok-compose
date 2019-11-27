# tivok-compose
Hosting and serving all the Tivok-Components together. The software can be tested [online](https://fox.tivok.projects.pxheller.co).



## Prerequisites

Docker is required to build and run the project. Also, it's assumed that the [tivok-api](https://github.com/hellerphilipp/tivok-api) has already been built locally (or loaded from another source)



## Running Cleap

After cloning this repository and `cd`ing into the folder, a bit of configuration must be done for tivok apps to run successfully.

### Include tivok-fox

For the management frontend to work, the appropriate files need to be copied into `./www/fox/`. The files can be found in the [tivok-fox repository](https://github.com/hellerphilipp/tivok-fox).

The following files and folders are required:

* `index.html`
* `img/`
* `dist/`

### Configure the environment

To use the database and authenticate incoming requests, a bit of configuration is needed. You just need to fill out the tenant and create a password for the database:

```bash
cp .env.example .env && vim .env
```

The `POSTGRES_PASSWORD_PROD` can be freely chosen, but the `JWT_TENANT` needs to match the configuration included in [tivok-fox](https://github.com/hellerphilipp/tivok-fox).



### Configure hostnames

Since the project consists of a frontend as well as a backend which are both independently acccessed from outside docker's network, both require a seperate hostname.

While there are options to link the docker-compose network with the local network and access the containers via their IP addresses, this can cause inconsistensies in configuration.

The hostnames can also be updated in `./nginx/nginx.conf` (referred to as `server_name`). Using the default `JWT_TENANT`, configured in `.env` as well as in [tivok-fox](https://github.com/hellerphilipp/tivok-fox), the hostname for the frontend must either match `localhost:8000` or `fox.tivok.projects.pxheller.co:80` (default). If `localhost` should be used, the the authentication callback-url needs to be updated in [tivok-fox](https://github.com/hellerphilipp/tivok-fox) and the exposed port must be changed in `docker-compose.yml`.

The hostname for the backend can be changed freely, but must be updated in [tivok-fox](https://github.com/hellerphilipp/tivok-fox).

For simplicity, it's recommended to stick with the default hostnames `fox.tivok.projects.pxheller.co` as well sa `api.tivok.projects.pxheller.co`, and make them resolve to `127.0.0.1` via the hosts file using:

```bash
sudo vim /etc/hosts
```

On non-unix systems, this command might look different. For the change to take effect, it might be required to [flush your systems dns cache](https://www.google.com/search?q=flush%20dns%20cache).



### Generate SSL-keys

Security is important for tivok and all outbound traffic will only be served via https. In order to test everything locally, you can either change the nginx config located in `/nginx/nginx.conf`, or generate your own keys:

```bash
openssl req -x509 -nodes -newkey rsa:1024 -days 1 -keyout './keys/fox.tivok.projects.pxheller.co/privkey.pem' -out './keys/fox.tivok.projects.pxheller.co/fullchain.pem' -subj '/CN=localhost'
```

```bash
openssl req -x509 -nodes -newkey rsa:1024 -days 1 -keyout './keys/api.tivok.projects.pxheller.co/privkey.pem' -out './keys/api.tivok.projects.pxheller.co/fullchain.pem' -subj '/CN=localhost'
```

These will only last for a day, but feel free to change the parameters.



### Running the services

Once everything is set up, the services can be started using docker-compose:

```bash
docker-compose up
```



## Versioning

All Tivok-projects use [SemVer](http://semver.org/) for versioning. For released versions, see the [tags on this repository](https://github.com/hellerphilipp/tivok-compose/releases).
