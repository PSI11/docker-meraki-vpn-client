docker-meraki-vpn-client
===
[![](https://images.microbadger.com/badges/image/ubergarm/l2tp-ipsec-vpn-client.svg)](https://microbadger.com/images/nicolabeghin/docker-meraki-vpn-client) [![](https://images.microbadger.com/badges/version/ubergarm/l2tp-ipsec-vpn-client.svg)](https://microbadger.com/images/nicolabeghin/docker-meraki-vpn-client) [![License](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/nicolabeghin/docker-meraki-vpn-client/blob/master/LICENSE)

A tiny Alpine based docker image to quickly setup a Meraki VPN client w/ PSK.

## Motivation
Does your office or a client have a VPN server already setup and you
just need to connect to it? Do you use Linux and are jealous that the
one thing a MAC can do better is quickly setup this kind of VPN? Then
here is all you need:

1. VPN Server Address
2. Pre Shared Key
3. Username
4. Password

## Run
Create `docker-compose.yml`

    version: '2'
    services:
      meraki-vpn-client:
        image: psi11/docker-meraki-vpn-client
        env_file: secrets.env
        privileged: true
        container_name: meraki-vpn-client
        hostname: meraki-vpn-client
        volumes:
          - /lib/modules:/lib/modules:ro

Setup credentials in `secrets.env`

    VPN_SERVER_IPV4=x.x.x.x
    VPN_PSK=meraki
    VPN_USERNAME=myuser@myhost.com
    VPN_PASSWORD=mypass
    
Now run it:

    docker-compose up

## Route
From the host machine configure traffic to route through VPN link:

    # confirm the ppp0 link and get the peer e.g. (192.0.2.1) IPV4 address
    ip a show ppp0
    # route traffic for a specific target ip through VPN tunnel address
    sudo ip route add 1.2.3.4 via 192.0.2.1 dev ppp0
    # route all traffice through VPN tunnel address
    sudo ip route add default via 192.0.2.1 dev ppp0
    # or
    sudo route add -net default gw 192.0.2.1 dev ppp0
    # and delete old default routes e.g.
    sudo route del -net default gw 10.0.1.1 dev eth0
    # when your done add your normal routes and delete the VPN routes
    # or just `docker stop` and you'll probably be okay

## Test
You can see if your IP address changes after adding appropriate routes e.g.:

    curl icanhazip.com

## References
* [forked from `ubergarm/l2tp-ipsec-vpn-client`](https://github.com/ubergarm/l2tp-ipsec-vpn-client)
* [Meraki on Mint](http://stuffjasondoes.com/2018/08/16/configuring-meraki-client-vpn-on-linux-mint-19-network-manager/)
* [L2TP / IPsec VPN on Ubuntu 16.04](http://www.jasonernst.com/2016/06/21/l2tp-ipsec-vpn-on-ubuntu-16-04/)
