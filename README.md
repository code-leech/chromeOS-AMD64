# How to run AMD64 apps on a Chromebook

## Feel free to skip this rant [here](https://github.com/code-leech/chromeOS-AMD64#setup)
Chromebooks are kinda annoying, as they tend to be either ARM chipsets or x86 chipsets. This means that you can't play most videogames or most computer apps.

Luckily, after finding **NO FREAKING WAY TO INSTALL AMD64 APPS ON MY STUPID CHROMEBOOK**, I came up with a solution:

~~Buy a new computer~~ Use [Distrobox](https://github.com/89luca89/distrobox) to emulate AMD64. Now, you may be asking:

> oH CoDeLEcH wY u No USe bOx64?

First of all... SHUT UP! 

[box64](https://github.com/ptitSeb/box64) is NOT HELPFUL when it comes to chromebooks (i.e. useless) thanks to chromeOS' "LINUX" (in **speech marks**) being more limited than a man with a broken neck, thanks to the root directory **NOT BEING EDITABLE**. 

And thanks to the chromebook community for **NOT COMING UP WITH A SOLUTION**.

So i spent the whole of my **FREE TIME** to make a fix that probably no-one will use.

Rant over.

# Setup:

Please don't make fun of me for explaining everything in this tutorial.

## Requirements:

* Basic knowledge of linux commands
* If you are scared of terminals, don't be! If you just copy and paste, nothing will go wrong.
* A decent chromebook

## Step 1:

Open terminal in your app menu. If not set up, feel free to do so. Go ahead with all the defaults.

Pin the app to your shelf (the bottom bar on your chromebook)

Once opened (and set up), execute the command below:

```
sudo apt install uidmap podman -y
```

After this, close terminal (do **NOT** shut it down).

## Step 2:

Now we need to open a little something called **CROSH**. Whilst that sounds kinda scary, it's just the linux terminal of chromeOS, albeit a little bare-bones.

To open crosh, press **CTRL + ALT + T**

Copy and paste these commands one by one.

```
vsh termina
```
```
lxc exec penguin -- /bin/sh -c "printf '%s\n' '1000:100000:65536' | tee /etc/subuid /etc/subgid"
```

After this, close CROSH.

## Step 3:

Open terminal again.

Luckily for you, there is only one command, but you need to **COPY AS IT IS, AS A WHOLE**.

```
mkdir ~/.local
mkdir ~/.local/bin
PATH=$PATH:~/.local/bin
git clone https://github.com/89luca89/distrobox.git
cd distrobox
./install
```

Keep terminal open.

## Step 4:

To configure podman (the system behind the emulation) we need to install nano, using the terminal command below:

```
sudo apt install nano -y
```
```
sudo nano /etc/containers/registries.conf
```

For those of you who are unfamiliar with the nano commands, use **CTRL + O** save and exit. Use enter key to select options.

Navigate your way to the line saying `## unqualified-search-registries = ["example.com]`, and replace the line (delete hashtags too) with the below:

`unqualified-search-registries = ["docker.io"]`

Press **CTRL + O** so save and exit.

After this, close terminal (do **NOT** shut it down).

## Step 5:

With podman and distrobox successfuly installed, we only need to install the emulator.

Re-open crosh with **CTRL + ALT + T** as we did earlier.

Now run these commands:

```
vmc container termina penguin --privileged true
```
```
sudo apt install qemu-user-static binfmt-support
```

Close CROSH.

Now right click the terminal icon in your shelf and select **POWER OFF**.

## Step 6:

With all the installation complete, open terminal again and run:

```
podman pull --arch=amd64 registry.fedoraproject.org/fedora-toolbox:latest
```
```
distrobox create -i fedora-toolbox fedora
```
```
distrobox enter fedora
```

Please note that the first time you run the previous command it will take a lot of time, but this is only once.

You can now close terminal any time without it harming your system.

# Usage:

To enter the subsystem, run

```
distrobox enter fedora
```

To exit simply run 

```
exit
```

# How to install Apps:

### Please note that this is only compatible with .rpm files, and uses dnf instead of apt.

To install an app, enter the subsystem and install it there.

To export apps to your main system, there are 2 kinds of export:

**--bin**

**--app**

If it is a desktop app, run:

```
distrobox-export --app app_name
```

If it it is a command, first find the location of the binary. Copy the output.

```
which commandname
```
```
distrobox-export --bin /which/command/output

And there we go. AMD64 apps on a chromebook.
