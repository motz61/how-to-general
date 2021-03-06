###### [How-To] Privoxy Server Creation + Skype behind Proxy

---
# Table of Contents

* [1.0 Intro](#10-intro)
* [1.1 Minimal Privoxy server creation (on FreeBSD)](#11-minimal-privoxy-server-creation-on-freebsd)
* [1.2 Privoxy Installation](#12-privoxy-installation)
* [1.3 Privoxy Configuration](#13-privoxy-configuration)
* [1.4 Starting Up Privoxy](#14-starting-up-privoxy)
* [2.0 Setting Skype up for the proxy](#20-setting-skype-up-for-the-proxy)
* [2.1 Skype behind the Tor Network](#21-skype-behind-the-tor-network)
* [2.2 How to find public proxy servers](#22-how-to-find-public-proxy-servers)

---
## 1.0 Intro

This guide will explain the following two things:

* Minimal Privoxy server creation
* Using skype behind a proxy server

---
## 1.1 Minimal Privoxy server creation (on FreeBSD)

Even though the explanation will cover only the configuration on FreeBSD, you can use Privoxy on any unix-based systems.

Privoxy is a simple solution to create a proxy server.

We could also use Squid or other alternatives, but it would be time-consuming trying to use 'em for such a trivial reason.

---
## 1.2 Privoxy Installation

You can easily install Privoxy from the pre-compiled packages:

```shell
$ pkg search privoxy
$ pkg install privoxy-454353
```

---
## 1.3 Privoxy Configuration

Now, you need to specify both IP and Port to use inside the file "/usr/local/etc/privoxy/config".

Find this line:

`listen-address  127.0.0.1:8118`

and change it with:

`listen-address  111.222.333.444:666`

_Note: 111.222.333.444 should be the real machine's IP, and 666 the relative port used by privoxy to listen all the incoming connections._

---
## 1.4 Starting Up Privoxy

You should now run the privoxy service, and start using it.
You have two ways:

* Opening "/usr/rc.conf" and adding the following line inside of it:

  ```shell
  privoxy_enable="YES"
  ```

  Then you can start the service doing:

  ```shell
  $ service privoxy start
  ```

  _Note: Adding such a line will let the system run privoxy at boot time._

* Simply:

  ```shell
  $ service privoxy onestart
  ```

  _Note: You will have to run this every time you reboot your server._

The proxy is now ready to be used.

---
## 2.0 Setting Skype up for the proxy

As you could imagine, Skype has tons of issues.

Even the proxy settings bar has many horror stories behind.

Note: The proxy settings have been fucked up in the new skype versions, so downgrade to 7.15.0.102 if you want to keep using this method.

---
If you dare to use the **Options->Advanced->Connection** option for it, you'll be reconnected through your real IP if skype can't connect to the proxy.

_tl;dr If someone ddosses your proxy, your skype will show your real IP to your contacts, and then they could directly attack you._

_Note: If your proxy is down, your skype will still go online, and **your real IP will be shown to everyone**._

Luckily, Skype supports an alternative and concealed way through windows registry modifications. (??? rly)

_Note: If your proxy is down, your skype will stay totally offline, and your real IP **won't** be shown to anyone._

You need to create a .reg file, run it, restart Skype.exe, and then everything will go fine.

You can use [this site](https://dl.dropboxusercontent.com/u/33446/twitch/skype.html) (suggested by the EdgyShadow) to auto-create such file, otherwise you can do it manually.

---
I usually use these three files to enable/disable the proxy on skype, or either use HTTPS or SOCKS:

* [Skype__AddProxy_HTTPS.reg](./Skype__AddProxy_HTTPS.reg)
```ini
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Skype\Phone]
"DisableSupernode"=dword:00000001
"ProxySetting"="HTTPS"
"DisableUDP"=dword:00000001
"ProxyAddress"="111.222.333.444:666"
"ProxyUsername"=-
"ProxyPassword"=-
```

* [Skype__AddProxy_SOCKS5.reg](./Skype__AddProxy_SOCKS5.reg)
```ini
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Skype\Phone]
"DisableSupernode"=dword:00000001
"ProxySetting"="SOCKS5"
"DisableUDP"=-
"ProxyAddress"="111.222.333.444:666"
"ProxyUsername"=-
"ProxyPassword"=-
```

* [Skype__RemoveProxy.reg](./Skype__RemoveProxy.reg)
```ini
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Skype\Phone]
"DisableSupernode"=-
"ProxySetting"=-
"ProxyAddress"=-
"DisableUDP"=-
"ProxyUsername"=-
"ProxyPassword"=-
```

I have used this configuration since 2013, and I never got any problems with it. The rest is up to you.

_Note: Privoxy doesn't support user authentication, but you can set a range of allowed IPs from the setting file._

_Note2: This guide can work with every proxy server, either privoxy's or public one._

---
## 2.1 Skype behind the Tor Network

Another way to proxy skype is to use the tor network as proxy.

You just need to run the [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en), run [Skype__AddProxy_SOCKS5_tor.reg](./Skype__AddProxy_SOCKS5_tor.reg), and restart the skype process:

```ini
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Skype\Phone]
"DisableSupernode"=dword:00000001
"DisableUDP"=-
"ProxySetting"="SOCKS5"
"ProxyAddress"="127.0.0.1:9150"
"ProxyUsername"=-
"ProxyPassword"=-
```

If you want to run Tor without starting Firefox:

1. Switch folder `cd .\Tor Browser\Browser\TorBrowser\Tor`
1. Run it as `tor.exe -f ..\Data\Tor\torrc-defaults`

Or make a [RunTorOnly.bat](./RunTorOnly.bat) using a full path like this: (adjust the path as you prefer)

```sh
TITLE Tor
".\Tor Browser\Browser\TorBrowser\Tor\tor.exe" -f ".\Tor Browser\Browser\TorBrowser\Data\Tor\torrc-defaults"
```

_Note: if tor.exe was still running, the .bat will close after some moments. You can safely close the console and tor.exe will keep running fine._

---
## 2.2 How to find public proxy servers

There are several sites containing many lists of proxy servers. One of them is [HMA - Proxy List](https://www.hidemyass.com/proxy).
