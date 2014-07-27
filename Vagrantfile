VAGRANTFILE_API_VERSION = "2"

MEMORY = 2048
CPU_COUNT = 2
$script1 = <<SCRIPT
##sudo apt-get -y remove grub-pc
##sudo apt-get -y install grub-pc
##sudo grub-install /dev/sda # precaution
##sudo update-grub 
sudo yum -y install git
wget http://peak.telecommunity.com/dist/ez_setup.py
sudo python ez_setup.py
sudo easy_install pip
sudo easy_install --upgrade pip
wget https://bitbucket.org/pypa/setuptools/raw/0.8/ez_setup.py -O - | sudo python
SCRIPT

$script = <<SCRIPT

##
## Install system pre-requisites
##


##
## Clone the configuration repository and run Ansible
##
#echo "Now to Download the configuration"
cd /var/tmp
git clone https://github.com/rayhooker/wordpress-ansible.git

##
## Install the ansible requirements
##
cd /var/tmp/wordpress-ansible
sudo rpm -Uvh http://download.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm
sudo yum install gmp-devel -y
##yum install ansible -y
sudo pip install ansible

##
## Run the edx_sandbox.yml playbook in the configuration/playbooks directory
##
cd /var/tmp/wordpress-ansible
sudo ansible-playbook -c local ./site.yml -i "localhost," -vvv
SCRIPT

edx_platform_mount_dir = "edx-platform"
forum_mount_dir = "cs_comments_service"
ora_mount_dir = "ora"
edx_configuration_dir = "configuration"
edx_theme_dir = "edx-theme"

if ENV['VAGRANT_MOUNT_BASE']

  edx_platform_mount_dir = ENV['VAGRANT_MOUNT_BASE'] + "/" + edx_platform_mount_dir
  forum_mount_dir = ENV['VAGRANT_MOUNT_BASE'] + "/" + forum_mount_dir
  ora_mount_dir = ENV['VAGRANT_MOUNT_BASE'] + "/" + ora_mount_dir

end

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Creates an edX devstack VM from an official release
  config.vm.box     = "virtualbox-centos6.4-wordpress"
  config.vm.box_url = "http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-i386-v20131103.box"
  #config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network :private_network, ip: "192.168.35.10"
  config.vm.network :forwarded_port, guest: 80, host: 8800 
  config.ssh.insert_key = true
  config.vm.boot_mode = gui

  # Enable X11 forwarding so we can interact with GUI applications
  if ENV['VAGRANT_X11']
      config.ssh.forward_x11 = true
  end

  config.vm.synced_folder "wordpress", "/srv/wordpress", :create => true, nfs: true
  #config.vm.synced_folder "#{edx_configuration_dir}", "/edx/app/edx_ansible/edx_ansible", :create => true, nfs: true
  #config.vm.synced_folder "#{edx_platform_mount_dir}", "/edx/app/edxapp/edx-platform", :create => true, nfs: true
  #config.vm.synced_folder "#{forum_mount_dir}", "/edx/app/forum/c_comments_service", :create => true, nfs: true
  #config.vm.synced_folder "#{ora_mount_dir}", "/edx/app/ora/ora", :create => true, nfs: true
  #config.vm.synced_folder "#{edx_theme_dir}", "/edx/app/edxapp/themes/sas", :create => true, nfs: true

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", MEMORY.to_s]
    vb.customize ["modifyvm", :id, "--cpus", CPU_COUNT.to_s]

    # Allow DNS to work for Ubuntu 12.10 host
    # http://askubuntu.com/questions/238040/how-do-i-fix-name-service-for-vagrant-client
    vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
  end

  # Assume that the base box has the edx_ansible role installed
  # We can then tell the Vagrant instance to update itself.
  config.vm.provision "shell", inline: $script1
  config.vm.provision "shell", inline: $script

end
