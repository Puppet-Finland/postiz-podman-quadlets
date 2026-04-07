# postiz-podman-quadlets

Set of Podman systemd unit definitions ("Quadlets") for running Postiz, a
social media management tool. These files we initially created with
[Podlet](https://github.com/containers/podlet) from the docker-compose.yml file
in [postiz-docker-compose](https://github.com/gitroomhq/postiz-docker-compose),
followed by quite heavy manual improvements.

These Quadlet files run fine with rootless podman.

# Usage

We recommend running these services as a dedicated non-root user.

Create a new system user:

    $ useradd postiz
    $ passwd postiz

Create a global systemd Quadlet directory for the user:

    $ mkdir -p /etc/containers/systemd/users/$(id -u postiz)

Copy all the files from this repository to the Quadlet directory:

    $ cp *.container *.volume *.network /etc/containers/systemd/users/$(id -u postiz)/

Enable linger for the user:

    $ loginctl enable-linger postiz

Then log out and log back in as the *postiz* user.

Create the volumes:

    $ for volume in postiz-redis temporal-postgresql temporal-elasticsearch temporal-dynamicconfig postiz-config postiz-uploads postiz-postgresql; do podman volume create $volume; do

Create the networks:

    $ for network in postiz temporal; do podman network create $network; done

Reboot, then log back in as *postiz*. You should see Postiz running at port 4007.

Note that you need to configure TLS *or* use a SSH tunnel and access postiz at
http://localhost:4007. Otherwise the browser's authentication cookie will be
rejected as it's passed through an unencrypted connection.
