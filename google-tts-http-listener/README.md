Google TTS HTTP Listener
======

## About
This add-on provides an HTTP server that serves up Google TTS files from HA.

This allows you to keep your HA configuration using HTTPS but allow your Google home devices to correctly work.

## Instructions
1. Enable my custom repository in HassIO [using instructions provided here](https://github.com/mljenkinsdotcom/hassio-addons)
2. Install the Google TTS HTTP Listener add-on from within the HA interface
3. Start the add-on
4. Modify your HA `configuration.yaml` alter the `tts`, `google_translate` section to `http://(IP of HA):8080`

Example configuraton.yaml file:
```yaml
# Text to speech
tts:
  - platform: google_translate
    service_name: google_say
    base_url: http://192.168.0.55:8080
```

## How It Works
When Google makes a TTS call, it must make a call to an HTTP server or an HTTPS server with a valid cert that matches the full domain name of the request.

When you enable HA with a valid cert, chances are the cert's subject for the hostname and domain are only accessible externally and will not resolve correctly internally.

There are several ways to get around this, like blocking Google's DNS servers and using your own DNS internally.  However, these setups are cumbersome and may not be available to a lot of users using dumbed down network equipment.

When Google TTS makes a call, you can configure the `base_url` to be something different than your regular HA listener.

Hence, a call from a Google home device will look something like this (if it were hitting an Apache server):
```
192.168.0.55 - - [23/May/2019:14:43:22 -0400] "GET /api/tts_proxy/0ce8efd3b65c24df111c5ba68410665f06d91550_en_-_google_translate.mp3 HTTP/1.1" 404 576 "-" "Mozilla/5.0 (X11; Linux armv7l) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.47 Safari/537.36 CrKey/1.39.154941"
```

What this add-on does is make the URI path `/api/tts_proxy` available on an HTTP port.

Note there is no authentication, authorization, nor encryption on this.
However, typically that would be the same case if you expose HA via HTTP.
Hence, this add-on allows you to keep the native HA HTTPS configuration to keep HA secure, but only expose the MP3 for Google TTS to work.

In summary, we will serve up an HTTP server that will expose the directory `/config/tts` on this add-on's file system to the URI `/api/tts_proxy`

Note you can change the port `8080` to be anything you want.
Just change it on the add-on configuration base and modify your `base_url` value accordingly.

