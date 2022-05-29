---
back: '/cvm'
---
This is a copy-paste of my guide originally added to the [unofficial collabvm forum](https://collabvm.outofmemory.online/index.php?threads/how-to-set-up-your-own-uservm.9/).

So you losers want to set up a UserVM? There are a couple of things you need.


- A computer with decent hardware. You can barely get by with 2GB per VM if you're running something like Linux or Windows 7 in the VM. You'll need a fast CPU too.
- Linux. For this guide I'm assuming you use Debian or a Debian-based distro like Ubuntu or Mint. Technically CollabVM can be run on Windows but why would you ever want to attempt that? If you don't have Linux but want to install it [URL='https://debian.org/']you can get Debian here[/URL]
- Computer and linux literacy. You must be comfortable with the command line.


Ok, strap in.

First let's get the source code. Install git if you don't have it. Debian command:

```bash
sudo apt-get install git
```

Then clone the source code. We'll be using yellows111's fork because it has more features. Clone it with:

```bash
git clone https://github.com/yellows111/collab-vm-server --depth 1 ~/collabvm
```

That'll download the source code to the collabvm folder in your home directory. CD to it

```bash
cd ~/collabvm
```

Now install some dependencies:

```bash
git submodule init
git submodule update
sudo bash scripts/grab_deps_linux.sh
```

That should install all the dependencies. If you're not using Debian that last command won't work so you're gonna have to install them manually. Here are the dependencies for you to install, however your distro does that:

- Boost 1.67 or above (1.67 is untested, 1.71 onwards works fine though. 1.75+ recommended)
- libvncserver
- libpng
- libturbojpeg
- libsqlite3-dev
- cairo


Have fun tracking those down.
Next, time to compile. On debian the build-essential package has everything you need to compile C code. On other distros you basically just need gcc and make.

```bash
sudo apt-get build-essential
```

Finally, compile the source code. The command is so complicated it'd take soooo long to type. It's:

```bash
make
```

It should be successful. If not, sucks to suck. Just kidding, leave a comment below and we'll try to help.

Now you have the collabvm server successfully installed. There should be a collab-vm-server file in ~/collabvm/bin, as well as a http_src directory in ~/collabvm.
Now, create a directory to house collabvm, and cd to it. I like to put mine in /collabvm, but your choice really.

```bash
mkdir /collabvm
cd /collabvm
```

Copy the collabvm executable:

```bash
cp ~/collabvm/bin/collab-vm-server /collabvm/collab-vm-server
```

Now you need to host the admin panel. The server used to do this itself but someone made the braindead decision of gutting that functionality (jk, I'm sure there was a reason). If you already have a webserver installed, copy the contents of ~/collabvm/http_src to it. Otherwise, install one like this on Debian:

```bash
sudo apt-get install nginx
sudo rm -rf /var/www/html/*
sudo cp -r ~/collabvm/http_src/. /var/www/html/
sudo systemctl enable nginx
sudo systemctl start nginx
```

Now, if you browse to [URL='http://127.0.0.1/admin/']127.0.0.1/admin/[/URL], you should just see "Loading" forever. That's because collabvm hasn't been started yet. First decide on a port other than 80 for collabvm to run on. Then, edit the file /var/www/html/admin/admin.min.js (Or wherever your webserver stores files) as root with the editor of your choice. Control+F to find the part that says:

```
wss://":"ws://")+serverAddress+"/")
```

and replace it with:

```
wss://":"ws://")+serverAddress+":6001/")
```

Replace 6001 with the port you chose. Or just keep it.
Now you can run collabvm. Later we'll setup a systemd service but for now just use the terminal. Open a new tab/window and run:

```bash
sudo /collabvm/collab-vm-server 6001
```

Again, replace 6001 with the port of your choice. Must be the same as the one you used earlier.
Refresh your browser. You should now see a login screen. The default password is "collabvm" without quotes. Change it immediately on logging in. You now have the oppurtunity to go wild and change any settings you want. When you're done, it's time to set your first vm up.

First, install qemu:

```bash
sudo apt-get install qemu
```

Then, make a directory for your hard disks and create your first hard drive.

```bash
mkdir collabvm/img
qemu-img create -f qcow2 /collabvm/img/vm1.qcow2 100G
```

Don't worry, this won't use up 100GB. The hard disk will start as a few kb and expand as it needs more space.
Now, download the iso for whatever OS you want your vm to have, and start the vm with qemu:

```bash
qemu-system-x86_64 -hda /collabvm/img/vm1.qcow2 -accel kvm -cpu host -m 4G -usbdevice tablet -device e1000,netdev=lan -netdev user,id=lan -cdrom /path/to/your/installer.iso
```

That startup command should work well with any modern OS, like WinXP and up or a modern linux distro. Older OS's will need special configuration not covered here. Really though just google "Install (os) in qemu"
You now should have a QEMU window open. You can now install your OS, and go wild making any modifications you want. When you're ready, we can add the VM to your collabvm server.
Shut down the VM and your terminal should return. Now, return to the browser and click New VM. Set your configuration as follows:

- Auto Start: Check if you want your vm to autostart when cvm starts
- URL name: Something unique for each vm. used by the backend
- Display Name: What people see on the uservm homepage.
- MOTD: Welcome message when people open your VM. HTML formatting is fully supported.
- VNC address: 127.0.0.1
- VNC port: Something unique for each VM. Port must be at least 5900
- Anything QMP: Leave as-is
- Hypervisor: QEMU
- Start command: Copy and paste the command you used earlier to start your vm, minus the -cdrom argument
- Snapshot mode: Hard drive snapshots
- Restore after shutdown: Your preference. Controls if the vm automatically restores when CVM server stops.
- Turns enabled: checked. Unchecking makes the vm unusable
- Turn time: your choice
- Votes enabled: Check if you want people to be able to vote to reset. If not you'll have to be online to manually reset whenever someone forkies it.
- Vote time: your choice
- Vote cooldown time: your choice.
- Agent enabled (and everything under it): Uncheck unless you're both insane and have agent installed


Now save vm settings and start the vm. Your vm is now there, but you can't see it until you install the webapp. Let's do that now.

Clone the webapp, and download node.js, npm, and gulp:

```bash
sudo apt-get install nodejs npm
sudo npm install --global npm gulp-cli
git clone https://github.com/yellows111/collab-vm-web-app/ --depth 1 ~/cvmwebapp
cd ~/cvmwebapp
```

Now, open ~/cvmwebapp/src/js/collab-vm/common.js with the editor of your choice. Find

```
serverAddress: "localhost:6004",
```

And replace it with:

```
serverAddress: window.location.hostname + ":6001",
```

Use the port you used earlier instead of 6001 (unless you **did** use 6001, of course). The window.location.hostname makes the webapp work from anywhere instead of just your local computer.
Now, make sure you're cd'd to ~/cvmwebapp, and run another **very**complex compile command:

```bash
gulp
```

Now delete the placeholder files in your webserver and copy the webapp:

```bash
sudo rm /var/www/html/* # This won't remove the admin folder because we didn't use the -r flag
sudo cp -r ~/cvmwebapp/build/. /var/www/html/
```

Now browse to [URL='http://127.0.0.1']127.0.0.1[/URL] and you should see, in all it's glory, your vm. Play around with it all you want, and repeat the steps above to add more vms.

So, you probably don't want to have to run it in a console 24/7. That's where systemd comes in. Stop your cvm server with ctrl+c in the window it's in. Then, create the file /etc/systemd/system/collabvm.server as root and paste the following in. Note that if you're using a distro without systemd you'll have to figure out how to do this with your init system.

```
[Unit]
Description=CollabVM Server
Requires=nginx.service
[Service]
ExecStart=/collabvm/collab-vm-server --port 6001
KillSignal=SIGINT
PIDFile=/collabvm/collabvm.pid
WorkingDirectory=/collabvm
[Install]
WantedBy=multi-user.target
```

Replace nginx.service with your web server if it's something other than nginx (like apache2).
Now, reload systemd, set the server to start on boot (unless you don't want it to) and start it.

```bash
sudo systemctl daemon-reload
sudo systemctl enable collabvm # Omit this to not start on boot. Note that if you do you'll have to run the below command every time.
sudo systemctl start collabvm
```

If you browse to 127.0.0.1, your server should be up again. Congratulations you made a collabvm instance.

Now the last thing is getting it on uservm. Join the [URL='https://discord.gg/ebaCsRrEry']Discord[/URL] if you haven't already and just harass wcry in #support to add your vm to uservm until they put it up.

Congratulations you have a uservm. At some point I might make a guide on how to use Bridged networking with TAPs so you can have things like a web filter and active directory. No promises.
