<img src="https://github.com/italia/spid-graphics/blob/master/spid-logos/spid-logo-b-lb.png" alt="SPID" data-canonical-src="https://github.com/italia/spid-graphics/blob/master/spid-logos/spid-logo-b-lb.png" width="500" height="98" />

[![Join the #spid-wordpress channel](https://img.shields.io/badge/Slack%20channel-%23spid--wordpress-blue.svg?logo=slack)](https://developersitalia.slack.com/messages/C7ESTBB98)
[![Get invited](https://slack.developers.italia.it/badge.svg)](https://slack.developers.italia.it/)
[![SPID on forum.italia.it](https://img.shields.io/badge/Forum-SPID-blue.svg)](https://forum.italia.it/c/spid)
[![Build Status](https://travis-ci.com/simevo/spid-wordpress-example-form.svg?branch=master)](https://travis-ci.com/simevo/spid-wordpress-example-form)

# spid-wordpress-example

Demo WordPress site with SPID plugin

## Installation with docker-compose

The quickest way to start an instance of WordPress with the [**SPID WordPress** plugin](https://github.com/simevo/spid-wordpress) and the [SPID test Identity Provider spid-testenv2](https://github.com/italia/spid-testenv2) all configured and set up is using Docker Compose.

You will need:
- Docker CE
- Docker Compose
- git, wget, curl, make and openssl
- on macOS to make sure the `wget` and [`envsubst`](https://stackoverflow.com/questions/23620827/envsubst-command-not-found-on-mac-os-x-10-8) commands are available:
    ```sh
    brew install gettext
    brew link --force gettext
    ```

To make the image building process faster, pull in advance the required docker images (this may take some time):
```sh
docker-compose pull
docker pull wordpress:cli
```

Before starting up, edit the `.env` file if you wish to change the host names.

To start up clone the 2 repos one inside the other:
```sh
git clone https://github.com/simevo/spid-wordpress-example
cd spid-wordpress-example
git clone https://github.com/simevo/spid-wordpress.git spid-wordpress # yes ! clone spid-wordpress inside spid-wordpress-example !
make
docker-compose up --build
```

Wait until messages stop, then in a separate shell issue the command:
```sh
make post
```

Your brand new WordPress site will be at: http://localhost:8099; log in at http://localhost:8099/wp-login.php as admininstrator with user = `test2` and password = `test3`.

Your SPID test IdP will be at: http://localhost:8088

To deactivate / reactivate the plugin use:
```sh
make activate
make deactivate
```

To remove the containers and default network, but preserve the database: `docker-compose down`

To remove all: `docker-compose down --volumes`

## Refreshing key/certificate pairs

If on atteping a SPID login to WordPress you get "Errori di validazione. Il certificato è scaduto.", refresh key/certificate pairs for SP and IdP:
```
openssl req -x509 -nodes -sha256 -subj '/C=IT' -newkey rsa:2048 -keyout idp_conf/idp.key -out idp_conf/idp.crt
rm sp_conf/wp.key sp_conf/wp.crt
touch rm sp_conf/wp.key sp_conf/wp.crt
chmod o+w sp_conf/wp.key sp_conf/wp.crt
make refresh # refreshes the SP metadata to the IdP and vice versa
```

## Authors

Emanuele Aina, Giulio Gatto and Paolo Greppi

## License

Copyright (c) 2018-2020, simevo s.r.l.

License: AGPL 3, see [LICENSE](LICENSE) file.
