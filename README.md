# My Console Setup on Windows

## Requirements

1. [Virtualbox](https://www.virtualbox.org/) and [Vagrant](https://www.vagrantup.com/) 
2. [Ubuntu box](https://app.vagrantup.com/ubuntu/boxes/xenial64) for Vagrant
3. [ConEmu](https://conemu.github.io/en/Downloads.html)
4. [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
5. [PuTTY Color Themes](https://github.com/AlexAkulov/putty-color-themes) - I don't use any 

## Steps

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

## Final Result
