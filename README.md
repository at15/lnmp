# lnmp
Vagrant box for PHP projects

Deprecated, see https://github.com/dyweb/miemieda

For PHP5.X see [legacy/ubuntu-14.04-PHP5](https://github.com/at15/lnmp/tree/legacy/ubuntu-14.04-PHP5) branch.
The [feature/php7](https://github.com/at15/lnmp/tree/feature/php7) branch is also deprecated, PHP7 is now in the master branch.

- [Vagrant Cloud PHP7.x](https://atlas.hashicorp.com/at15/boxes/lnmp7/)
- [Vagrant Cloud PHP5.x](https://atlas.hashicorp.com/at15/boxes/lnmp/)

## Include

- Ubuntu 16.04 LTS
- Nginx 1.10.1 `ppa:nginx/stable`
- MySQL 5.7
- PHP 7.1 `ppa:ondrej/php`
- Composer `/usr/local/bin/composer`
- Redis 3.07 `ppa:chris-lea/redis-server`
- Phpmyadmin 4.5.4.1 (nginx config is `/etc/nginx/conf.d/phpmyadmin.conf`)

## Requirement

- Virtualbox 4.3+ (5.0 is tested, the guest addtion is working on windows, Mac user may
need to update TODO: the issue for brew cask installed vbox)
- Vagrant latest
- Host OS: Fedora 25, Ubuntu 16.10, Windows 10 (not tested but should work)

## Usage

### Use the pre-built box

Deprecated, this repo will be moved to https://github.com/dyweb, the box on Atlas will also change.

Use the following Vagrantfile, and run `vagrant up --provider virtualbox`

- edit your host machine's `hosts` file, add `127.0.0.1    mysql.lk`, and use `mysql.lk:8080` to visit phpmyadmin
- use `localhost:8080` to visit nginx welcome page

If you have a bad network, you can run the provison multiple times, it has side effects, but won't break your
PHP projects.

- `vagrant up --provison` works when your vm is stop or running
- `vagrant reload --provision` would restart the vm and run the provision when start

As for package management tools, you will have problem running `npm` inside the box
when your using windows host due to symbolic link.

- npm can be solved using `npm install --no-bin-links` see https://github.com/npm/npm/issues/5874
- composer may have similar problem

For windows users, I suggest running `git` and `npm` in your git bash or cmd instead of inside the vm.
Since most PHP projects use node for building assets instead of application server, running instead vm
is not a must.

````ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|

  # The pre built box is on https://atlas.hashicorp.com/at15/boxes/lnmp7/
  config.vm.box = "at15/lnmp7"

  # If the base box updated, you can't update your current vm without destroy it
  config.vm.box_check_update = true

  # Forward guest 80 to host 8080
  config.vm.network "forwarded_port", guest: 80, host: 8080

  # Use 777 for the default mount folder. which works for Ubuntu and Mac, windows always got 777
  config.vm.synced_folder ".", "/vagrant", \
    :disabled => false, \
    :mount_options  => ['dmode=777,fmode=777']

  config.vm.provider "virtualbox" do |vb|
     # Don't show the GUI unless you have some bug
     vb.gui = false
     # Customize the amount of memory on the VM:
     vb.memory = "1024"
     # Config the name
     vb.name = "lnmp7"
  end
end
````

### Build the box

- `git clone git@github.com:at15/lnmp.git`
- `cd lnmp`
- `vagrant up` it will run the provision using Ansible. see [site.yml](site.yml) for detail
- ssh into the vm and run `util/clean.sh` to clean up apt stuff before you package the box
- `vagrant package` to build the new box
- upload it to [Atlas](https://atlas.hashicorp.com/boxes/search?utm_source=vagrantcloud.com&vagrantcloud=1)

## Acknowledgement

- https://github.com/LukeXuan/atlas-centos7
- https://github.com/leonidas/ansible-nvm
- https://github.com/arbabnazar/ansible-roles/tree/master/LEMP

## License

MIT
