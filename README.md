# Setup of a TURN and STUN server for WORKADVENTURE with COTURN

Here you find instructions to setup a turn stun server to allow peer-to-peer WebRTC connections that bypass strict configured firewalls. 

Without this, the nice sponaneous meetings "on the way" that Workadventure offers will not work for users behind some networks. 

This in the case you use an external instance of JitsiMeet or of the BigBlueButton for video conferences. If you want to self host also JistiMeet or BBB you will need such a service also for them, but this would be another tutorial.

I use `docker` and even better `docker compose`, since I find much cleaner to have all of the settings documented in a `docker-compose.yml` file than having to script a long docker command.

#### What you will find here
* a `docker-compose.yml` file and a configuration file to bring up a turn and stun servers listening at port 433 with TLS with coturn [https://github.com/coturn/coturn]

#### What do you need
* A server with a publicly reachable IP address.
* I used as host name a subdomain of a domain I control, it should be possible to do without it, the only problem being, obtaining a certificate from *let's encrypt*. This should be possible using `nip.io` and using a HTTP-01 challenge as opposed to a DNS-01 challenge, see [https://letsencrypt.org/docs/challenge-types/] (TODO: this needs to be verified)
* Clone this repository in your server on which you are running docker compose and have a user in the the `docker` and `sudo`groups.
* Obtain a certificate with let's encrypt for your server. TODO I still to have to work out a clean and direct way to do this and also renew the certificate... without making my life easier with Traefik Proxy [https://traefik.io/traefik/]. A suitable setting for Coturn with Trafik I have not beeing able to work out yet. I am happy for any input, everyone seem to leave this as TODO...

#### What to do once you have the list above:
* add the certificate and pkey from let's encrypt in the root directory of the repository as `turn_server_cert.pem` and `turn_server_pkey.pem` (of course you can use another directory, just change the volume to mount them accordingly)
