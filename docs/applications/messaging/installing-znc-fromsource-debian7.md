---
author:
    name: Alex Fornuto
    email: docs@linode.com
description: 'Using the ZNC bouncer to retain an IRC connection'
keywords: 'znc,irc,debain,source'
license: '[CC BY-ND 3.0](http://creativecommons.org/licenses/by-nd/3.0/us/)'
modified: Friday, January 9th, 2015
modified_by:
    name: 'Elle Krout'
published: 'Friday, August 21, 2014'
title: 'Installing ZNC from Source on Debian 7.5'
---

ZNC is an IRC bouncer. It's designed to run on a server that remains connected to an IRC network and buffer messages, so that a local IRC client can connect and disconnect without losing a chat session or missing any messages. In this guide we'll go over installing ZNC from source and configuring the daemon.

{: .note}
>This guide is written for a non-root user. Commands that require elevated privileges are prefixed with ``sudo``. If you're not familiar with the ``sudo`` command, you can check our [Users and Groups](/docs/tools-reference/linux-users-and-groups) guide.


Prepping the system
-------------------

1. Start by making sure your system is up to date:

        sudo apt-get update
        sudo apt-get upgrade

2. Since we're installing from source, we'll need the `build-essential` and `checkinstall` packages:

       sudo apt-get install build-essential checkinstall

3. If you want to use SSL encryption to connect to the web interface (recommended) you'll also need to install `libssl-dev`:

       sudo apt-get install libssl-dev

Installation
------------

The commands listed below reference ZNC version 1.4, which is the latest at the time of publishing. Please refer to [ZNC](http://wiki.znc.in/ZNC)'s official website for the latest version and download URL.

1. Download the latest version of ZNC:

        wget http://znc.in/releases/znc-1.4.tar.gz

2. Expand the archive file:

       tar -xvf znc-1.4.tar.gz

3. Move into the ZNC directory:

        cd znc-1.4

4. Run the `configure` script to make sure your Linode has all the necessary prerequisites:

        ./configure

   If not, you will need to install whichever package the script references as missing before continuing.

5. Run the following two commands to install ZNC to your Linode:

        make
        sudo checkinstall --fstrans=0 make install

   {: .note }
   > The program `checkinstall` will create a `.deb` package which you can use to reinstall this version of ZNC in the future. It has it's own set of options to review. If you prefer, you can instead run `sudo make install` to install ZNC as is.


Configuration
-------------


To begin configuring ZNC, run the following command:

    znc --makeconf

This will launch an interactive script asking you for input on a variety of parameters. Below is an example output of the `makeconf` script with standard options selected. To match your needs, you can use or change the provided input at your discretion. If you're not sure, use the default option. Many of these options can be adjusted later through the web interface.

{:.note}
> Make sure to change the Username variable.


        [ .. ] Checking for list of available modules...
        [ >> ] ok
        [ ** ] Building new config
        [ ** ]
        [ ** ] First let's start with some global settings...
        [ ** ]
        [ ?? ] What port would you like ZNC to listen on? (1025 to 65535): 5678
        [ ?? ] Would you like ZNC to listen using SSL? (yes/no) [no]: yes
        [ ?? ] Would you like ZNC to listen using both IPv4 and IPv6? (yes/no) [yes]:
        [ .. ] Verifying the listener...
        [ >> ] ok
        [ ** ]
        [ ** ] -- Global Modules --
        [ ** ]
        [ ** ] +-----------+----------------------------------------------------------+
        [ ** ] | Name      | Description                                              |
        [ ** ] +-----------+----------------------------------------------------------+
        [ ** ] | partyline | Internal channels and queries for users connected to znc |
        [ ** ] | webadmin  | Web based administration module                          |
        [ ** ] +-----------+----------------------------------------------------------+
        [ ** ] And 9 other (uncommon) modules. You can enable those later.
        [ ** ]
        [ ?? ] Load global module <partyline>? (yes/no) [no]:
        [ ?? ] Load global module <webadmin>? (yes/no) [no]: yes
        [ ** ]
        [ ** ] Now we need to set up a user...
        [ ** ]
        [ ?? ] Username (AlphaNumeric): user
        [ ?? ] Enter Password:
        [ ?? ] Confirm Password:
        [ ?? ] Would you like this user to be an admin? (yes/no) [yes]:
        [ ?? ] Nick [user]: user
        [ ?? ] Alt Nick [user_]:
        [ ?? ] Ident [user]:
        [ ?? ] Real Name [Got ZNC?]:
        [ ?? ] Bind Host (optional):
        [ ?? ] Number of lines to buffer per channel [50]:
        [ ?? ] Would you like to clear channel buffers after replay? (yes/no) [yes]:
        [ ?? ] Default channel modes [+stn]:
        [ ** ]
        [ ** ] -- User Modules --
        [ ** ]
        [ ** ] +--------------+------------------------------------------------------------------------------------------+
        [ ** ] | Name         | Description                                                                              |
        [ ** ] +--------------+------------------------------------------------------------------------------------------+
        [ ** ] | chansaver    | Keep config up-to-date when user joins/parts                                             |
        [ ** ] | controlpanel | Dynamic configuration through IRC. Allows editing only yourself if you're not ZNC admin. |
        [ ** ] | perform      | Keeps a list of commands to be executed when ZNC connects to IRC.                        |
        [ ** ] | webadmin     | Web based administration module                                                          |
        [ ** ] +--------------+------------------------------------------------------------------------------------------+
        [ ** ] And 20 other (uncommon) modules. You can enable those later.
        [ ** ]
        [ ?? ] Load module <chansaver>? (yes/no) [no]: yes
        [ ?? ] Load module <controlpanel>? (yes/no) [no]:
        [ ?? ] Load module <perform>? (yes/no) [no]:
        [ ?? ] Load module <webadmin>? (yes/no) [no]: yes
        [ ** ]
        [ ?? ] Would you like to set up a network? (yes/no) [no]: yes
        [ ?? ] Network (e.g. `freenode' or `efnet'): oftc
        [ ** ]
        [ ** ] -- Network Modules --
        [ ** ]
        [ ** ] +-------------+-------------------------------------------------------------------------------------------------+
        [ ** ] | Name        | Description                                                                                     |
        [ ** ] +-------------+-------------------------------------------------------------------------------------------------+
        [ ** ] | chansaver   | Keep config up-to-date when user joins/parts                                                    |
        [ ** ] | keepnick    | Keep trying for your primary nick                                                               |
        [ ** ] | kickrejoin  | Autorejoin on kick                                                                              |
        [ ** ] | nickserv    | Auths you with NickServ                                                                         |
        [ ** ] | perform     | Keeps a list of commands to be executed when ZNC connects to IRC.                               |
        [ ** ] | simple_away | This module will automatically set you away on IRC while you are disconnected from the bouncer. |
        [ ** ] +-------------+-------------------------------------------------------------------------------------------------+
        [ ** ] And 14 other (uncommon) modules. You can enable those later.
        [ ** ]
        [ ?? ] Load module <chansaver>? (yes/no) [no]: yes
        [ ?? ] Load module <keepnick>? (yes/no) [no]: yes
        [ ?? ] Load module <kickrejoin>? (yes/no) [no]:
        [ ?? ] Load module <nickserv>? (yes/no) [no]: yes
        [ ?? ] Load module <perform>? (yes/no) [no]: yes
        [ ?? ] Load module <simple_away>? (yes/no) [no]: yes
        [ ** ]
        [ ** ] -- IRC Servers --
        [ ** ] Only add servers from the same IRC network.
        [ ** ] If a server from the list can't be reached, another server will be used.
        [ ** ]
        [ ?? ] IRC server (host only): irc.oftc.net
        [ ?? ] [irc.oftc.net] Port (1 to 65535) [6667]: 6697
        [ ?? ] [irc.oftc.net] Password (probably empty):
        [ ?? ] Does this server use SSL? (yes/no) [no]: yes
        [ ** ]
        [ ?? ] Would you like to add another server for this IRC network? (yes/no) [no]:
        [ ** ]
        [ ** ] -- Channels --
        [ ** ]
        [ ?? ] Would you like to add a channel for ZNC to automatically join? (yes/no) [yes]: yes
        [ ?? ] Channel name: #linode
        [ ?? ] Would you like to add another channel? (yes/no) [no]:
        [ ?? ] Would you like to set up another network? (yes/no) [no]: no
        [ ** ]
        [ ?? ] Would you like to set up another user? (yes/no) [no]:
        [ .. ] Writing config [/home/user/.znc/configs/znc.conf]...
        [ >> ] ok
        [ ** ]
        [ ** ]To connect to this ZNC you need to connect to it as your IRC server
        [ ** ]using the port that you supplied.  You have to supply your login info
        [ ** ]as the IRC server password like this: user/network:pass.
        [ ** ]
        [ ** ]Try something like this in your IRC client...
        [ ** ]/server <znc_server_ip> 4567 user:<pass>
        [ ** ]And this in your browser...
        [ ** ]http://<znc_server_ip>:4567/
        [ ** ]
        [ ?? ] Launch ZNC now? (yes/no) [yes]: yes
        [ .. ] Opening config [/home/user/.znc/configs/znc.conf]...
        [ >> ] ok
        [ .. ] Loading global module [webadmin]...
        [ >> ] [/usr/local/lib/znc/webadmin.so]
        [ .. ] Binding to port [4567]...
        [ >> ] ok
        [ ** ] Loading user [user]
        [ ** ] Loading network [oftc]
        [ .. ] Loading network module [chansaver]...
        [ >> ] [/usr/local/lib/znc/chansaver.so]
        [ .. ] Loading network module [keepnick]...
        [ >> ] [/usr/local/lib/znc/keepnick.so]
        [ .. ] Loading network module [nickserv]...
        [ >> ] [/usr/local/lib/znc/nickserv.so]
        [ .. ] Loading network module [perform]...
        [ >> ] [/usr/local/lib/znc/perform.so]
        [ .. ] Loading network module [simple_away]...
        [ >> ] [/usr/local/lib/znc/simple_away.so]
        [ .. ] Adding server [irc.oftc.net 6697 ]...
        [ >> ] ok
        [ .. ] Loading user module [chansaver]...
        [ >> ] [/usr/local/lib/znc/chansaver.so]
        [ .. ] Loading user module [perform]...
        [ >> ] [/usr/local/lib/znc/perform.so]
        [ .. ] Loading user module [webadmin]...
        [ >> ] [/usr/local/lib/znc/webadmin.so]
        [ .. ] Forking into the background...
        [ >> ] [pid: 12784]
        [ ** ] ZNC 1.4 - http://znc.in


Once you've completed the configuration and launched ZNC, you can access the web interface by going to your Linode's IP address in your web browser. Be sure to specify the port you defined during the configuration script and prefix it with `https://` .

{: .note}
>If the [Firewall portion](/docs/security/securing-your-server#creating-a-firewall) of the [Securing Your Server](/docs/securing-your-server/) guide has been completed, add a line to `/etc/iptables.firewall.rules` allowing traffic to your IRC port.

[![ZNC's Web Admin](/docs/assets/znc-web-admin_small.png)](/docs/assets/znc-web-admin.png)

Connecting The Client
---------------------

You can use any preferred GUI or CLI client to connect to ZNC. For the example below we'll be using

[Colloquy](http://colloquy.info/).

1. Under the **file** menu, select **New Connection**. If you've just opened Colloquy for the first time, this window should open automatically.

   [![Colloquy Menu](/docs/assets/colloquy-new-connection.png)](/docs/assets/colloquy-new-connection.png)

2. Fill out the New Connection window with the relevant information used during your configuration of ZNC.


3. Press **Connect**. You should be connected to your ZNC server and from there to any networks and channels you've configured to autojoin.


SSL Encryption with a Signed Certificate (Optional)
---------------------------------------------------

If you would like to use a signed certificate to encrypt your connection to ZNC, you can do so by adding your key and certificate to the `znc.pem` file:

    cat domain.key domain.crt > znc.pem



