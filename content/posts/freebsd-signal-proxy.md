---
title: "Setting up a Signal Proxy using FreeBSD"
date: 2021-02-05T20:00:00-08:00
showDate: true
---

With the events that
[the private messaging app Signal has been blocked in Iran](https://www.aljazeera.com/news/2021/1/26/iran-blocks-signal-messaging-app-after-whatsapp-exodus),
Signal has come up with an "proxy" solution akin to
[Tor](https://www.torproject.org)'s
[Bridges](https://tb-manual.torproject.org/bridges/), and have given
instructions on how to do it.

For people who prefer FreeBSD over Linux like myself, we obviously can't run
Docker, which is what Signal's instructions focus on.

Fortunately, the Docker image is just a fancy wrapper around nginx,
[and the configs can be ported to any OS](https://twitter.com/ahfaeroey/status/1357878573785362437).
Here, I'll show you how to set up a Signal Proxy on FreeBSD.

# Prerequisites

You will need a FreeBSD server that is not running anything on Port 80 or 443.
If you don't run FreeBSD, you can substitute the commands with ones specific to
your OS or Linux distro.

You can also substitute `acme.sh` with another ACME-compatible Let's Encrypt
client, or use another CA. I used `acme.sh` with ZeroSSL, but won't describe
the latter here for simplicity's sake.

# Steps

First, you'll need to install `acme.sh` and `nginx`:

    pkg install acme.sh nginx

Then, you'll need to get an SSL certificate:

    acme.sh --register-account -m neel@neelc.org --server zerossl
    acme.sh --issue --standalone -d DOMAIN

Replace `DOMAIN` with the hostname you want to use.

Next, use the following config for `/usr/local/etc/nginx/nginx.conf`:

    load_module /usr/local/libexec/nginx/ngx_stream_module.so;
    user www;

    events {}

    stream {
        map $ssl_preread_server_name $name {
            textsecure-service.whispersystems.org    signal-service;
            storage.signal.org                       storage-service;
            cdn.signal.org                           signal-cdn;
            cdn2.signal.org                          signal-cdn2;
            api.directory.signal.org                 directory;
            contentproxy.signal.org                  content-proxy;
            uptime.signal.org                        uptime;
            api.backup.signal.org                    backup;
            sfu.voip.signal.org                      sfu;
            updates.signal.org                       updates;
            updates2.signal.org                      updates2;
            default                                  deny;
        }

        upstream relay {
             server localhost:4433;
        }

        upstream signal-service {
             server textsecure-service.whispersystems.org:443;
        }

        upstream storage-service {
            server storage.signal.org:443;
        }

        upstream signal-cdn {
            server cdn.signal.org:443;
        }

        upstream signal-cdn2 {
            server cdn2.signal.org:443;
        }

        upstream directory {
            server api.directory.signal.org:443;
        }

        upstream content-proxy {
            server contentproxy.signal.org:443;
        }

        upstream backup {
            server api.backup.signal.org:443;
        }

        upstream sfu {
            server sfu.voip.signal.org:443;
        }

        upstream updates {
            server updates.signal.org:443;
        }

        upstream updates2 {
            server updates2.signal.org:443;
        }

        upstream deny {
            server 127.0.0.1:9;
        }
    
        server {
            listen                443 ssl;
            proxy_pass            relay;

            access_log            off;
            error_log             /dev/null;

            ssl_certificate     /root/.acme.sh/DOMAIN/fullchain.cer;
            ssl_certificate_key /root/.acme.sh/DOMAIN/DOMAIN.key;
         }

        server {
            listen                4433;
            proxy_pass            $name;
            ssl_preread           on;
            error_log             /dev/null;
            access_log            off;
         }
    }

Again replace `DOMAIN` with the domain your Signal proxy is on.

After you have set the `nginx` config, enable it via sysrc:

    sysrc nginx_enable=YES

And start it:

    service nginx start

You should have a fully-functioning Signal proxy.

# Share the proxy

While the official Signal instructions are very Docker-centric for the tech
part, it does have other useful information on sharing the proxy securely.
This means I won't duplicate the information here.

[Here's the subsection on Signal's website on sharing the proxy](https://signal.org/blog/help-iran-reconnect/#iranasignalproxy).
