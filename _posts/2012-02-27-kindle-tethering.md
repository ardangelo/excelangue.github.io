---
layout: post
title: Tethering Kindle 3
categories: [hacks]
---

[Featured on Hackaday!](http://hackaday.com/2012/02/27/tethering-a-kindle-for-free-3g/)


What you need:

* Modify Headers for Firefox 
* A Kindle Keyboard 3G 
* [These files]({{ site.url }}/assets/files/kindle3g.zip)
* Jailbroken 
* USB Network Hack applied 
* tcpdump-arm in the Kindle's USB storage (X-FSN method 1 only) 

*Warning!* The process by which one harnesses the Kindle's free 3G is a violation of the Kindle 3G's terms and agreements. In addition, the key used to access 3G is tied directly to your unique Kindle. All traffic obtained in this manner is sent through Amazon's proxy server. Amazon knows who you are.

Update 2014: The OS X instructions are written for OS X Leopard. The prompts may have changed.

Enable debug mode and usbNetwork by typing in these commands in the search box:

	;debugOn
	~usbNetwork

Plug your Kindle into the target computer. The "USB Connected" screen should no longer appear on the Kindle. Instead, a message should appear on your computer noting that a new device has been connected. If no message appears, open System Preferences, Network Pane, and click the Plus sign under the list of devices. Select these options:

![Interface: Ethernet Adaptor en3 Service Name: Kindle]({{ site.url }}/assets/images/kindle-tethering/eth3.png)

The name is not needed, but is recommended.

After the Kindle interface is set up, configure DCHP like so:

![Configure: Manually IP Address: 192.168.2.1]({{ site.url }}/assets/images/kindle-tethering/net-set.png)

Manually, with an IP address of 192.168.2.1. Other fields are not needed.

We will now proceed to find your "X-FSN key" that the Kindle uses to identify you to the network.

## X-FSN Method 1 (Difficult, quicker)

If you have Windows, method 2 is easier.

Remember to enable Wireless in the Kindle, and check that a 3G signal is present before proceeding.

After these steps are completed, open a new Terminal window (You can press Cmd-Space, and search for Terminal with Spotlight). Enter these commands:

	telnet 192.168.2.2
	/mnt/us/tcpdump-arm -nAi ppp0 -s0

Afterwards, your window should look similar to the results below:

![Welcome to Kindle!]({{ site.url }}/assets/images/kindle-tethering/tcpdump.png)

Do not close this window! Proceed by browsing to a site on your Kindle's browser. After it is done loading, go back to tcpdump-arm. There should be a lot of cryptic code. Use Cmd-F `x-fsn` to find your X-FSN key:

![x-fsn]({{ site.url }}/assets/images/kindle-tethering/x-fsn.png)

Keep this window open. Start your Firefox browser, then open Modify Headers. Get your x-fsn key from tcpdump-arm and add them to each page. Windows users need to use the Modify option for all keys except x-fsn, and also need to select "always on" in the Information tab.

Add these keys as well:
	User-Agent: Mozilla/5.0 (Linux; U; en-US) AppleWebKit/528.5+ (KHTML, like Gecko, Safari/528.5+) Version/4.0 Kindle/3.0 (screen 600x800; rotate)
	Accept: application/xml,application/xhtml+xml,text/html;q=0.9,text/plain;q=0.8,image/png,*/*;q=0.5
	Accept-Encoding: gzip

![Headers windows]({{ site.url }}/assets/images/kindle-tethering/headers.png)


After this is complete, go back to tcpdump-arm. If you get an error that says "Connection closed by remote host", that means you left it alone for too long. That is fine, just run

	telnet 192.168.2.2

to get back into the Kindle.

## X-FSN Method 2 (Easier)

This method works best with Windows, because there is no need to stop the connection at the right time to catch the key in the scrollback buffer in CMD (OS X keeps a huge scrollback buffer).

Telnet into the Kindle with

	telnet 192.168.2.2

You should now be presented with the Kindle shell. Enter the following series of commands to copy the key file to the USB storage folder:

	cd /
	cd /var/local/java/prefs/cookies/
	cp Cookie__x* /mnt/us/

Note the two underscores of `Cookie__x*`

Disable USB Network mode as you enabled it: enter `~usbNetwork` at the home screen. Unplug and replug the cable to bring the Kindle up as a disk drive. Open the file that starts with `Cookie__x-fsn` in a text editor. Your x-fsn key should be the first entry in the line. I got a "character not found" box at the end in Notepad, so be sure to remove that if you see it at the end.

Thanks to pupkin for this tip!

If this method does not work for you, method 1 is more reliable, but more complicated.

## Header Modification

Open up Modify Headers in Firefox and add this key:

	Action: Add 
	Name: x-fsn 
	Value:

Add these keys as well:
	User-Agent: Mozilla/5.0 (Linux; U; en-US) AppleWebKit/528.5+ (KHTML, like Gecko, Safari/528.5+) Version/4.0 Kindle/3.0 (screen 600x800; rotate)
 	Accept: application/xml,application/xhtml+xml,text/html;q=0.9,text/plain;q=0.8,image/png,*/*;q=0.5
 	Accept-Encoding: gzip
 	x-kn-appId: BBookletV3 (forgot this; thanks, Greg)

## OS X/Linux specific

Now that you're in the Kindle, run this command to set up a link to Amazon's proxy server:

	ssh -R 192.168.2.1:8099:fints-g7g.amazon.com:80 user@192.168.2.1

Replace "user" with your username on the target computer.

If you get an error whilst SSH'ing into the target computer, you might need to enable Remote Login (`System Preferences > Sharing > Remote Login`). You should then be faced with a prompt matching the target computer's. Run

	ping 192.168.2.2

to keep the telnet connection from timing out. Before running ping, your window should look something like this:

	![Do you want to continue connecting?]({{ site.url }}/assets/images/kindle-tethering/ssh.png)

## Windows specific

No need to keep the Telnet session open on Windows.

If you've already got SSH set up on your machine (you can run an SSH command from the command line), you can skip the install step.

To get SSH, install OpenSSH for Windows. Make sure to install "Client" mode. I think Server mode is optional, but you can install it anyway.

Test it out by opening the `Run...` window from the Start menu and typing `cmd`. After clicking OK a command line should appear. Enter `ssh`. The list of options and switches for SSH should pop up.

Now to set up Windows Firewall. Go to `Control Panel > Network > Windows Firewall` or search Firewall in Control Panel.

Add the server `C:\Program Files\OpenSSH\usr\sbin\sshd.exe` and the client `C:\Program Files\OpenSSH\bin\ssh.exe` to Windows Firewall exceptions.

In your command line window, go to your OpenSSH\bin folder with the `cd` command, followed by the path.

Enter these commands to set up groups and users:

	mkgroup -l >> ..\etc\passwd
	mkpasswd -l -u [username] >> ..\etc\passwd

To start the server, enter `net start opensshd`.

Now to set up the Ethernet modes.

Connect the Kindle to your computer, and wait for the drivers to install.

After the interface is connected, go to Network Connections.

Right click on the interface, and select Properties. Double click on "Internet Protocol" (Version 4 may be present). Use the following IP address: `192.168.2.1`, with a subnet mask of `255.255.255.0`. DNS server doesn't matter.

In your command window, enter `ssh -L 8099:fints-g7g.amazon.com:80 root@192.168.2.2`. The connection might take up to a minute or two, just wait a bit. If it asks you if you want to trust the key pair, type yes. You should be connected.

## Moving on

Now to set up Firefox. Open Firefox's proxy preferences (Firefox > Preferences > Advanced > Network > Change how Firefox connects to the Internet). Set the manual proxy to 127.0.0.1 on port 8099. The window should look like this:

![HTTP Proxy: 127.0.0.1 Port 8099]({{ site.url }}/assets/images/kindle-tethering/proxy-set.png)

Apply the settings, and you should be all set! You may have trouble using SSL, try to use http:// instead of https://.

Happy browsing (And try not to get caught ;-) )!

Thanks to Balaganov for the SSH information!

My advice is to use this only when in an emergency. Don't play Flash games or anything.

# Common errors:

* "There is a problem with your Kindle account...": You might not have set up the headers correctly. You successfully set up the link between your computer and Amazon's proxy servers, but they know you're not using your Kindle. Beware! This tips Amazon off that you're abusing your free 3G connection! 
* "The proxy server is refusing connections...": The SSH link you set up was improperly configured. To reset it, exit the constantly running ping command with Ctrl-C, then exit the SSH link. You should now be at the Kindle prompt. Press up to get the past SSH command. Double check that it looks like the command listed above, then execute it again, and start the ping command again. 
* The generic "Firefox can't connect..." error could mean that anything could have gone wrong. Double check all the steps. 