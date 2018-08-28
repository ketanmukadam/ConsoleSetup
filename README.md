# My Console Setup on Windows

## Requirements

1. [Virtualbox](https://www.virtualbox.org/) and [Vagrant](https://www.vagrantup.com/) 
2. [Ubuntu box](https://app.vagrantup.com/ubuntu/boxes/xenial64) for Vagrant
3. [ConEmu](https://conemu.github.io/en/Downloads.html)
4. [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
5. [PuTTY Color Themes](https://github.com/AlexAkulov/putty-color-themes) - I don't use any 

## Steps

### Vagrant Setup 
1. Install [Vagrant](https://www.vagrantup.com/) which provides an awesome command line utility for managing the lifecycle of virtual machines. Vagrant uses [Virtualbox](https://www.virtualbox.org/) which is a free virtualization software. 
   1. Once the software is installed, open a standard windows command prompt and type the following
   ```shell
      C:\> mkdir VagrantVMs
      C:\> cd VagrantVMs
      C:\VagrantVMs> vagrant init
      C:\VagrantVMs> vagrant box add ubuntu\xenial
   ```
   The exact name of the box to add can be obtained by searching the [catalogue](https://vagrantcloud.com/boxes/search). In the
   example above *"ubuntu"* is the username and *"xenial"* is the box. The **vagrant init** step creates a 
   [vagrantfile](https://www.vagrantup.com/docs/vagrantfile/)
2. Edit the [vagrantfile](https://www.vagrantup.com/docs/vagrantfile/) to use the added box and add shared folder
   ```ruby
      Vagrant.configure("2") do |config|
            config.vm.box = "ubuntu\xenial"
            config.vm.synced_folder "C:\\Ketan", "/ketan"
            config.vm.synced_folder "C:\\Users\\kmukadam\\Desktop", "/desk"
      end
   ```
3. Once the box has been configured, open a command prompt and cd into the Vagrant folder (described in Step 1)
   ```shell
      C:\VagrantVMs> vagrant up
      C:\VagrantVMs> vagrant ssh
   ```
4. This will bring up and VM and ssh into the box. 
   ```shell
      ubuntu@xenial:~$
   ```
   These base boxes live in your %userprofile%/.vagrant.d/boxes folder. You can list all installed boxes by typing vagrant box 
   list. You can delete boxes with vagrant box remove box/name.
   
5. The default *username* and *password* for VM box downloaded from vagrant website is **_vagrant/vagrant_**. By default it 
   uses a private key and hence following command can be used to ssh into the box
   ```shell
      ssh -l ubuntu -p 2222 -i .vagrant/machines/default/virtualbox/private_key 127.0.0.1
   ```
6. You can turn the VM off by using `vagrant halt`, or suspend it with `vagrant suspend`. Then turn it on again any time with
   `vagrant up`. Type `vagrant status` to see the current state of the VM.
7. Although the user `vagrant` has sudo access, in case you want to have root access to your VM box, you can change the SSH keys
   to grant root access, here is the script from [this blog post](http://jarrettmeyer.com/2016/11/28/root-login-with-vagrant)
   ```shell
   ROOT_HOME="/root"
   ROOT_SSH_HOME="$ROOT_HOME/.ssh"
   ROOT_AUTHORIZED_KEYS="$ROOT_SSH_HOME/authorized_keys"
   VAGRANT_HOME="/home/vagrant"
   VAGRANT_SSH_HOME="$VAGRANT_HOME/.ssh"
   VAGRANT_AUTHORIZED_KEYS="$VAGRANT_SSH_HOME/authorized_keys"

   # Setup keys for root user.
   ssh-keygen -C root@localhost -f "$ROOT_SSH_HOME/id_rsa" -q -N ""
   cat "$ROOT_SSH_HOME/id_rsa.pub" >> "$ROOT_AUTHORIZED_KEYS"
   chmod 644 "$ROOT_AUTHORIZED_KEYS"

   # Setup keys for vagrant user.
   ssh-keygen -C vagrant@localhost -f "$VAGRANT_SSH_HOME/id_rsa" -q -N ""
   cat "$VAGRANT_SSH_HOME/id_rsa.pub" >> "$ROOT_AUTHORIZED_KEYS"
   cat "$VAGRANT_SSH_HOME/id_rsa.pub" >> "$VAGRANT_AUTHORIZED_KEYS"
   chmod 644 "$VAGRANT_AUTHORIZED_KEYS"
   chown -R vagrant:vagrant "$VAGRANT_SSH_HOME"
   ```
   Point the script in the vagrantfile as below
   ```
      config.vm.provision "shell", path: "scripts/setup_ssh.sh"
   ```   
8. If you have a SSH client installed and set in the `PATH` then vagrant will use it, else vagrant comes with a pre-packaged 
   ssh client. The problem with any of these ssh clients is that they are all based on cygwin. I have found multiple issues 
   with terminal when cygwin based ssh client is used to connect to remote machines or with VM box (e.g. screen size changes 
   on switching from big monitor to laptop display or with scrolling or with color display). The best solution is to use 
   [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). 

9. To enable 'bridged' network (which means the VM will come up as a new machine on LAN network), enable the following in Vagrant file, which will instantiate another network interface (apart from the default NATed interface)
```
      config.vm.network "public_network"
```

### PuTTY Setup 

1. The installation of PuTTY is simple. I prefer portable version of PuTTY in a standard location (e.g. C:\Ketan\Softwares). Add
   its location in the PATH variable for vagrant to find it.  
2. Next step is to install [vagrant-multi-putty](https://github.com/nickryand/vagrant-multi-putty) plugin for vagrant. Just run
```shell
      $ vagrant plugin install vagrant-multi-putty
```
3. The advantage of `vagrant-multi-putty` plugin is that the SSH-keys are automatically converted to PuTTY style .ppk format
4. A simple way to use PuTTY now for ssh is to run this command
```shell
      vagrant putty <name of vm>
```
5. Note that PuTTY congiuration (mainly sessions) are stored in the registry under this key
```shell
      HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions
```
6. Some interesting [PuTTY Color Themes](https://github.com/AlexAkulov/putty-color-themes) can be installed and saved for
   different sessions.
7. Ensure to set the "Close window on exit" to **Always** as shown 
![alt text](https://github.com/ketanmukadam/ConsoleSetup/raw/master/Putty1.jpg "Putty Screenshot 1")
8. 

### ConEmu Setup 

The best terminal container so far that I tested is [ConEmu](https://conemu.github.io/en/Downloads.html). It is a super awesome piece of software with super-helpful developer. The only problem, if you hit an issue, there will be some solution but it has complex configuration to figure that out. Also since it is just a container, sometimes it is hard to know if the issue is with ConEmu or the underlying terminal software that is being used. 

My ConEmu [Configuration File](https://github.com/ketanmukadam/dotfiles/blob/master/ConEmu.xml)

**Problem 1**: Vagrant was using a cygwin based ssh (packaged with vagrant installer). It caused terminal & color issues (TERM=cygwin instead of xterm), especially with opening Vim in remote sessions. I replaced it with using PuTTY as default ssh client and now everything is smooth.

**Problem 2**: When I switched to PuTTY, the Ctrl-Tab stopped working for switching the tabs. It turns out that when any GUI application is run or attached (read focus) in ConEmu tabs – all keyboard keys are passed to this child GUI application. That's why Ctrl+Tab doesn't work in this case – it is processed by PuTTY but not ConEmu. 

As mentioned on this [SuperUser](https://superuser.com/questions/555809/conemu-switch-between-tabs-with-putty-tab-open#555848) page, Win+Q works, although a bit awkward to use. The default hot-key to switch focus between ConEmu and Child GUI is Win+z. It's a workable solution for me. 

### Misc Setup

The modern Linux system provide a mechanism called "network namespaces" which can be used to isolate networks on a single server. Some sample commands from [this unix stackexchange page](https://unix.stackexchange.com/questions/210982/bind-unix-program-to-specific-network-interface) 

```
# Add a new namespace called test_ns
ip netns add test_ns

# Set test to use eth0, after this point eth0 is not usable by programs
# outside the namespace
ip link set eth0 netns test_ns

# Bring up eth0 inside test_ns
ip netns exec test_ns ip link set eth0 up

# Use dhcp to get an ipv4 address for eth0
ip netns exec test_ns dhclient eth0

# Ping google from inside the namespace
ip netns exec test_ns ping www.google.co.uk
```


### Intersting Links

1. [Vagrant Manager](http://vagrantmanager.com/windows/) for Windows
2. 

## Final Result

![alt text](https://github.com/ketanmukadam/ConsoleSetup/raw/master/ConEmu.jpg "ConEmu Screenshot")
