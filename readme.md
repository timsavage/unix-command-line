## Contents

* [Audio, Video](#audio-video)
  * [Screen Recording](#screen-recording)
* [Fdisk](#fdisk)
* [Lshw](#lshw-hardware-lister-for-linux)
* [Macchanger](#macchanger)
* [Nethogs](#nethogs)
* [SSH](#ssh)
  * [Authentication Agent Forwarding](#ssh-authentication-agent-forwarding)
  * [Sshuttle](#ssh-sshuttle)
  * [Socks](#ssh-socks)
  * [Tunnel](#ssh-tunnel)
* [Scp](#scp-secure-copy)
* [Books](#books)
* [Awesome Lists](#awesome-lists)
* [Other Lists](#other-lists)

## Audio, Video (A/V) <a id="audio-video"/>

The `avconv` audio and video encoder is contained within the libav-tools package.

### Screen Recording <a id="screen-recording"/>

`-f` _"[Force](https://libav.org/avconv.html#Main-options) input or output file format. The format is normally autodetected for input files and guessed from file extension for output files."_ `x11grab` is the desktop format.  
`-s` specifies the width and height of the recorded area.  
`-r` is the frame rate.  
`-i` specifies the name of the input file, or in the below case, the `0.0` is display.screen number of your X11 server. This can work in conjunction with the `-f`. `0,0` is the x and y offset for grabbing.  
`nameOfFile.mov` is the name of the output file. `mov` is the quicktime format.
```bash
avconv -f x11grab -s hd1080 -r 10 -i :0.0+0,0 nameOfFile.mov
```
Useful resources:  
* [http://www.penguinproducer.com/Blog/2012/06/command-for-recording-desktop/](http://www.penguinproducer.com/Blog/2012/06/command-for-recording-desktop/)
* [http://wiki.oz9aec.net/index.php/High_quality_screen_capture_with_avconv](http://wiki.oz9aec.net/index.php/High_quality_screen_capture_with_avconv)
* [http://unix.stackexchange.com/questions/73622/how-to-get-near-perfect-screen-recording-quality](http://unix.stackexchange.com/questions/73622/how-to-get-near-perfect-screen-recording-quality)
* [https://libav.org/avconv.html](https://libav.org/avconv.html)

Screen recording can also be [done with VLC](http://www.howtogeek.com/120202/how-to-record-your-desktop-to-a-file-or-stream-it-over-the-internet-with-vlc/), including [setting the position and size](https://forum.videolan.org/viewtopic.php?t=101510) of the grab.

## [Fdisk]()

Shows partition information.
```bash
# Run command as root.
fdisk -l
```

## [Lshw: HardWare LiSter for Linux](https://github.com/lyonel/lshw) <a id="lshw-hardware-lister-for-linux"/>

Provides detailed information on the hardware configuration of the machine.
```bash
# Run command as root.
# Shows disk info.
# Example:
lshw -C disk
```
`-C`_`class`_ is an alias for `-class`_`class`_  
You can check all the _class_ types at the [home page](http://www.ezix.org/project/wiki/HardwareLiSter)  
The [man page](https://linux.die.net/man/1/lshw) is quite useful also.

## [Macchanger](https://github.com/alobbs/macchanger)

On a debian based system, install `macchanger` as root with:  
`apt-get install macchanger`  
Common usage example:
```bash
# Run commands as root.
# Syntax:
ifconfig <interface> down
macchanger -r <interface>
ifconfig <interface> up
```

## [Nethogs](https://github.com/raboof/nethogs)

On a debian based system, install `nethogs` as root with:  
`apt-get install nethogs`  
```bash
# Run command as root.
# Syntax:
nethogs [options] [device(s)]
# Example:
nethogs eth0
```
Some usage details [here](http://www.cyberciti.biz/faq/linux-find-out-what-process-is-using-bandwidth/)

## Scp: Secure copy <a id="scp-secure-copy"/>

`scp` uses `ssh` to copy.

`-p` preserves modification times, access times, and modes from the original file.  
`-r` is for recursion.  
`-P` Specify for non-default port.

Copy all files within a given directory from your local machine to a given directory on a remote host.
```bash
# Syntax:
scp -p -r -P <non-default-port> <given-directory>/* <your-file-server-account>@<file-server>:/file-server/path/<given-directory>/
```
Copy a given directory recursivly from remote host to within your current directory. Note the dot at the end of the command.
```bash
# Syntax:
scp -p -r -P <non-default-port> <your-file-server-account>@<file-server>:/file-server/path/<given-directory> .
```

## SSH

### Authentication Agent Forwarding <a id="ssh-authentication-agent-forwarding"/>

Using the `-A` argument allows you to forward the authentication agent connection. Essentially this allows you to hop (or pivot) from one ssh server to another.  
Example: laptop -> mail-server (through router) -> file-server  

You need to be aware that using agent forwarding opens an attack vector for  
"_users with the ability to bypass file permissions on the remote host (for the agent's Unix-domain socket) can access the local agent through the forwarded connection. An attacker cannot obtain key material from the agent, however they can perform operations on the keys that enable them to authenticate using the identities loaded into the agent._"  
> SSH man page

Example of copying a file from file-server (through router), to mail-server, to your laptop.

```bash
# In one terminal:
#    Find file first:
#    SSH to mail-server from WAN.
ssh <your-mail-server-account>@<router-wan-interface> -A -p <non-default-port>
#    From mail-server SSH to file-server.
ssh <your-file-server-account>@<file-server> -p <non-default-port>
#    Now you are on the file-server, locate the file to copy.
         
# In 2nd terminal: Copy from file-server to mail-server:
ssh <your-mail-server-account>@<router-wan-interface> -A -p <non-default-port>
scp -P <non-default-port> <your-file-server-account>@<file-server>:/file-server/path/filetocopy.txt ~/
         
# In 3rd terminal: Copy from mail-server to laptop:
scp -P <non-default-port> <your-mail-server-account>@<router-wan-interface>:/home/<your-mail-server-account>/filetocopy.txt /home/you/
```
Multi-hop [example](http://sshmenu.sourceforge.net/articles/transparent-mulithop.html).

### [Sshuttle](https://github.com/apenwarr/sshuttle/tree/master) <a id="ssh-sshuttle"/>

`sshuttle` combines an `ssh` tunnel with a system wide proxy, sometimes called a poor mans VPN.

On a debian based system, install `sshuttle` as root with:  
`apt-get install sshuttle`  
For a later version:  
`git clone git://github.com/apenwarr/sshuttle`  
After the server you intend to tunnel though has `sshd` running, from your local machine, run:  
```bash
# Syntax:
sshuttle --dns -vvr <proxy-user-account>@<proxy> 0/0
# Example:
sshuttle --dns -vvr kim@myproxy.net 0/0
```
Now all network traffic is `ssh` proxied through the `proxy` server, including DNS.

Test with: [http://ifconfig.me/](http://ifconfig.me/)  
Test if there is any leakage by visiting the DNS leak test [Web site](https://www.dnsleaktest.com/) and clicking on the Standard test button, you can also visit the IP/DNS Detect [site](http://ipleak.net/).

`-v` is just additional verbosity.  
`-r`, `--remote`_`=[username@]sshserver[:port]`_ _is the remote hostname and optional username and ssh port number to use for connecting to the remote server._  
`0/0` is short for `0.0.0.0/0`, which represents the subnets to route over the `ssh` tunnel. The usage of `0/0` routes all the traffic except DNS requests to the remote server.  
`--dns` will also proxy your DNS queries through the server you are tunneled to.

### Socks <a id="ssh-socks"/>
Setup socks proxy through `<proxy>`
```bash
ssh -D9090 <proxy-user-account>@<proxy>
```
Useful resource:  
* [http://linux.byexamples.com/archives/115/ssh-dynamic-tunneling/](http://linux.byexamples.com/archives/115/ssh-dynamic-tunneling/)

### Tunnel <a id="ssh-tunnel"/>
To forward (or tunnel) from one machine to another, you need to use the -L argument for Local port forwarding.  
`localhost` is the optional default `hostname`.  

```bash
# Syntax:
ssh -L [hostname:]<localport>:<localhost-name-on-remote-host>:<remoteport> <proxy-user-account>@<proxy>
# Example:
# Bind default proxy port (9090) to Tor port (9050) on proxy.net
# In this example, port 9050 is only accessible to the localhost interface of proxy.net
ssh -L 127.0.0.1:9090:127.0.0.1:9050 kim@myproxy.net
# If port 9050 was accessible to proxy.net interfaces other than localhost, then we could do the following:
ssh -L 127.0.0.1:9090:proxy.net:9050 kim@myproxy.net
```
From there, if Tor is listening on port `9050` of `proxy.net` and you set your browsers proxy to `localhost:9090` all browsing traffic will go through Tor, except DNS.

You can also do remote port forwarding by swapping the `-L` for `-R`, the rest of the command syntax stays the same. What this allows us to do is open a tunnel from a remote machine to our local machine. Further details can be seen [here](http://blog.trackets.com/2014/05/17/ssh-tunnel-local-and-remote-port-forwarding-explained-with-examples.html).

Details on tunneling RDP on my [blog post](https://blog.binarymist.net/2010/08/26/installation-of-ssh-on-64bit-windows-7-to-tunnel-rdp/).

You can also **combine tunnel with socks proxy**:
```bash
# Syntax:
ssh -D9090 -L 127.0.0.1:9090:127.0.0.1:9050 <proxy-user-account>@<proxy>
```

## Books


## Awesome Lists


## Other Lists

* []() - .

## License

[![Creative Commons License](http://i.creativecommons.org/l/by/4.0/88x31.png)](https://creativecommons.org/licenses/by/4.0/)

This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/)
