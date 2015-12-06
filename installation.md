###Creating an Ubuntu 14.04 Virtual Machine with Vagrant to Use GeoDjango

######Adapted from [vmenezes' and kaisaf's gearcircles](https://github.com/kaisaf/gearcircles/blob/master/instalation.txt)

Vagrant allows the user to work in Linux command line without needing to open a graphical virtual machine interface. ?CHECK VM REQS INSTALLATION?

#####Create and Connect to Virtual Machine

Install Vagrant and VirtualBox:
?ADD INSTALLATION?

Initialize your vagrant directory:

`$ vagrant init ubuntu/trusty64`

Add/turn on a virtual machine:

`$ vagrant up`

Connect to the virtual machine:

`$ vagrant ssh`

Now you are running an Ubuntu command line.


#####Set Up Virtual Machine

Install VirtualEnv and Python:
```bash
$ sudo apt-get install python-virtualenv
$ sudo apt-get install python3-dev
```

Install and configure PostgreSQL:
```bash
$ sudo apt-get install -y postgresql postgresql-contrib postgresql-server-dev-9.3
$ sudo -u postgres createuser -P [YOUR_USER_NAME]
$ sudo -u postgres createdb -O [YOUR_USER_NAME] [YOUR_DB_NAME]
```

Add permissions needed to run Django Tests:
```bash
$ sudo -u postgres psql
$ ALTER USER [YOUR_USER_NAME] CREATEDB;
$ ALTER ROLE [YOUR_USER_NAME] SUPERUSER;
$ \q
```

Check PostgreSQL:
```bash
$ psql -h localhost -U [YOUR_USER_NAME] [YOUR_DB_NAME]
$ \l
$ \q
```

Install and configure PostGIS:
```bash
$ sudo apt-get install -y postgis postgresql-9.3-postgis-2.1
$ sudo -u postgres psql -c "CREATE EXTENSION postgis; CREATE EXTENSION postgis_topology;" [YOUR_DB_NAME]
```

Install GeoDjango Library Requirements:

`$ sudo apt-get install libproj-dev gdal-bin`


#####Set a virtual network interface with its own IP and a shared folder

Exit and turn off your virtual machine:
```bash
$ exit
$ vagrant halt
```

Uncomment/edit the following lines in the Vagrantfile in your Vagrant directory:

`config.vm.network "public_network"`

`config.vm.synced_folder "/PROJECT/DIRECTORY/ON/HOME/COMPUTER", "/home/vagrant/PROJECTNAME"`

Turn on and check the IP of your virtual machine:
```bash
$ vagrant up
$ vagrant ssh
$ ifconfig
```

The IP Address to use is the second one down in the list of three. ?ADD IFCONFIG TEXT?

Start the server from this IP address:

`$ python3 manage.py runserver VIRTUAL_MACHINE_IP:8000`
