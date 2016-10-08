# macOS Ubuntu

This installs a Ubuntu 16.04 VM on macOS using [xhyve].

## Install xhyve

```
brew install xhyve
```

## Get booting kernel

```
./prepare.sh ~/Downloads/ubuntu-16.04-server-amd64.iso
```

## Create storage and boot to ISO

```
sudo ./create.sh ~/Downloads/ubuntu-16.04-server-amd64.iso
```

After booting, install Ubuntu just like you normally would. Your network
adapter will be in bridged mode.

Pro tip: Don't resize your terminal while you're going through the installer.

When you get to this question.

```
Install the GRUB boot loader to the master boot record?
```

Make sure you say yes.

### Grab newer kernel from install

When you get to "Installation complete", select "Go Back". Then, "Execute a
shell".

Find your current IP address. Remember, you're in bridged mode.

```
/sbin/ip addr show enp0s2
2: enp0s2: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast qlen 1000
    inet 192.168.64.5/24 brd 192.168.64.255 scope global enp0s2
```

Next, we're gonna copy some files back to the host. The exact name of the
following files might be slightly different, depending on when you download the
Ubuntu server ISO.

In the guest, run this.

```
cd /target/boot
cat initrd.img-4.4.0-21-generic | nc -l -p 1234
cat vmlinuz-4.4.0-21-generic | nc -l -p 1234
```

On the host, run this.

```
cd boot/
nc 192.168.64.5 1234 > initrd.img-4.4.0-21-generic
nc 192.168.64.5 1234 > vmlinuz-4.4.0-21-generic
```

Now, you can `exit` the shell and finish the installation.

## Start your new OS

```
sudo ./start.sh
```

## First steps

Here are some things you should probably do after logging in.

```
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install -y xterm
```

`xterm` is important because it'll install the `resize` command. You'll need to
manually resize the terminal dimensions because we're using a serial TTY or
something.

Everytime you resize your terminal, you need to run `resize`. Otherwise, your
output will get jacked.

## Reboot

That's it! You don't really need to reboot, but here's what that looks like.

```
jaime@xhyve:~$ sudo poweroff
[  636.675689] reboot: System halted
jaime@mac:~$ sudo ./start.sh
```

[xhyve]: https://github.com/mist64/xhyve