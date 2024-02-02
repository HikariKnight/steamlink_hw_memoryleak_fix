# A "fix" for the increasing input delay in that happens after you finish streaming a game on steamlink hardware
A simple restart after you are done streaming a game with your steamlink hardware

## Background
When Valve discontinued the original steamlink hardware, the last 2 updates (stable and beta/testing) had a bug inside the streaming_client binary that caused not all used memory to get freed up after you are done streaming a game. You can quickly test this by repeatedly starting and stopping a stream a few times, after about 3 ended streams, you will notice the UI getting progressively more and more sluggish.

This caused me to want to get into the steamlink (which thankfully you can enable an SSH server on and ssh into) and make a simple script to restart the steamlink after it was done streaming, as this would quickly flush the memory and guarantee a responsive UI and a better steamlink experience without delayed inputs caused by all the memory being used up.

## Why not just make sure the streaming_client was killed or stopped?
The binary does exit and terminate itself fine, however the memory is still stuck for some reason, hence the restart.

### Installation (setting up ssh)
1. Enable SSH on your steamlink by putting a file named `enable_ssh.txt` with some arbitrary data inside it, just **make sure it is not empty**.
2. Put the file on an USB inside `/steamlink/config/system/` on the usb this means you should have this path on your USB: `steamlink -> config -> system -> enable_ssh.txt`
3. Plug the USB into your steamlink and reboot it (or turn it on)
4. Once the steamlink has connected to the network, ssh will be enabled and you can unplug the USB

Now that we have SSH enabled, we need to do a few things
1. Connect to your steamlink using ssh from another computer `ssh root@steamlink` and the password `steamlink123` <br/>
NOTE: If you need your steamlinks ip address, you can find it in your router. If you have no access to your router, you can use [nmap](https://nmap.org/download.html) to scan your network using the command `nmap -sn 192.168.1.2-254` and you can just try the ip addresses reported back to you.
2. Run this command to make a backup of streaming_client since we will be replacing the binary, but we still need it accessible for our script <br/>
`cp /home/steam/bin/streaming_client /home/steam/bin/streaming_client-bin`<br>
NOTE: failing to make this copy of the binary before step 4 will soft brick your steamlink!
3. Exit the ssh shell by typing `exit`
4. Copy our script to the machine, make sure you have `cd` into the same directory as the streaming_client script from this repository, then run:<br/>
`scp ./streaming_client root@steamlink:/home/steam/bin/streaming_client`
5. SSH back into the steamlink and make sure the script is executable `ssh root@steamlink` followed by `chmod +x /home/steam/bin/streaming_client`
6. Restart the steamlink, from now on, each time you start streaming your computer to the steam link and you finish streaming, the steamlink will do a reboot to clear it's memory (your wireless controller should still be connected after the reboot has finished)

### Uninstallation
1. SSH into your steamlink with `ssh root@steamlink` and type in the password `steamlink123`
2. Run the command `cp /home/steam/bin/streaming_client-bin /home/steam/bin/streaming_client`
3. Exit the steamlink ssh shell by typing `exit`
