# Setup of a TURN and STUN server for WORKADVENTURE with COTURN

Here you find instructions to setup a turn stun server to allow peer-to-peer WebRTC connections that bypass strict configured firewalls. 

Without this, the nice sponaneous meetings "on the way" that Workadventure offers will not work for users behind some networks. 

This in case you use an external instance of JitsiMeet or of BigBlueButton for video conferences. If also self host JistiMeet or BBB you will need such a service anyway, but this would be another tutorial.

I use `docker` and even better `docker compose`, since I find much cleaner to have all of the settings documented in a `docker-compose.yml` file than having to script a long docker command.

#### What you find here
* a `docker-compose.yml` file and a configuration file `turnserver.conf` to bring up with coturn [https://github.com/coturn/coturn] turn and stun servers listening at port 433 with TLS. I based the configuration file on [https://github.com/coturn/coturn/blob/master/examples/etc/turnserver.conf] as on commit 41a8aa0 (Status 27/09/2022)

#### What you need to have
* A server with a publicly reachable IP address.
* Ideally have a domain you control to use as name for your server, it should be possible to do without it, the only problem being, obtaining a certificate from *let's encrypt* (https://letsencrypt.org/). This should be possible using `nip.io` (https://nip.io/) and a HTTP-01 challenge as opposed to a DNS-01 challenge, see https://letsencrypt.org/docs/challenge-types/ (TODO: this needs to be verified).
* Clone this repository in your server on which you are running docker compose and have a user in the the `docker` and `sudo` groups.
* Obtain a certificate for your server with let's encrypt. TODO I still to have to work out a clean and direct way to do this and also renew the certificate... without making my life easier with Traefik Proxy (https://traefik.io/traefik/). A suitable setting for Coturn with Traefik I have not beeing able to work out yet. I am happy for any input, everyone seem to leave this as a TODO... Possibility: look for a solution using haproxy (http://www.haproxy.org/) instead?

#### What to do once you have the above ingredients
* Add the certificate and pkey from let's encrypt in the root directory of the repository as `turn_server_cert.pem` and `turn_server_pkey.pem` (of course you can use another directory, just change the volume to mount them accordingly in `docker-compose.yml`)
* copy the template `turnserver.conf.template` to `turnserver.conf` and adjust some fields to your case. In particular you need to customize:
..* `external-ip=<HERE_IP_OF_YOUR_SERVER>`
..* add a user name and password in the form `user=yourusername:youruserpassword`
..* `realm=<YOUR_DOMAIN_OR_YOUR_IP.nip.io>`
* run `docker compose up -d` and you should have your server ready.
* In your Workadventure instance (see [https://github.com/pmaneggia/wa-workadventure]) bind your turn and stun server by setting these variables in `.env`
```
TURN_SERVER=turn:<THE_SAME_YOU_USED_IN_realm>:443
TURN_USER=yourusername
TURN_PASSWORD=youruserpassword
STUN_SERVER=stun:<THE_SAME_YOU_USED_IN_realm>:443
```
...Leave `TURN_STATIC_AUTH_SECRET=` empty, the turn server as configured here is not using a REST API.
* In your Workadventure instance run `docker compose down` followed by `docker compose up -d` for Workadventure to have the new values being used.
* YOU SHOULD BE DONE!

#### Checking and debugging

Thanks to [https://github.com/sh-csg] and [https://github.com/PhMemmel] for suggesting some of these!

##### Online tools to check turn and stun servers

* Tricle ICE [https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice]

* ICE test [https://icetest.info]

* How to test online if a stun-turn server is working properly or not **RULE OF THUMB**: (can be seen using  Trickle ICE or ICE test - from from  [https://ourcodeworld.com/articles/read/1526/how-to-test-online-whether-a-stun-turn-server-is-working-properly-or-not])
..* A STUN server works if you can gather a candidate with type "srflx".
..* A TURN server works if you can gather a candidate with type "relay".


##### Force the browser to "need" a TURN server for testing purposes
In FireFox you can force the conditions of needing a TURN server by setting in `about:config` the preference `media.peerconnection.ice.relay_only` to true (this forces the browser to only offer relay ICE candidates).