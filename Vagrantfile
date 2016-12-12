#This vagrantfile is used for testing out certificate-based SSH Ansible scripts.


$script= <<SCRIPT
sed -i 's/PasswordAuthentication no/#PasswordAuthentication no/g' /etc/ssh/sshd_config
systemctl restart sshd
useradd ansible
echo "ansible ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers
echo ansible | passwd -f --stdin ansible
SCRIPT

Vagrant.configure("2") do |config|

  config.vm.box = "centos/7"
  config.vm.synced_folder ".", "/vagrant", type: "virtualbox"
  config.vm.provision "shell",
    inline: $script

  config.vm.define "certAuthority" do |certAuthority|
    certAuthority.vm.network "private_network", ip: "10.100.0.20"
    certAuthority.vm.hostname = "certAuthority"
  end
  #
   config.vm.define "bastion01" do |bastion01|
     bastion01.vm.network "private_network", ip: "10.100.0.10"
     bastion01.vm.network "private_network", ip: "192.168.10.10"
     bastion01.vm.hostname = "bastion01"
   end

   config.vm.define "bastion02" do |bastion02|
     bastion02.vm.network "private_network", ip: "10.100.0.11"
     bastion02.vm.network "private_network", ip: "192.168.10.11"
     bastion02.vm.hostname = "bastion02"
   end

  #
  config.vm.define "web01" do |web01|
    web01.vm.network "private_network", ip: "10.100.0.101"
    web01.vm.hostname = "web01"
  end

  config.vm.define "web02" do |web02|
    web02.vm.network "private_network", ip: "10.100.0.102"
    web02.vm.hostname = "web02"
  end

  config.vm.define "db01" do |db01|
    db01.vm.network "private_network", ip: "10.100.0.121"
    db01.vm.hostname = "db01"
  end

 config.vm.define "ansible" do |ansible|
   ansible.vm.network "private_network", ip: "10.100.0.9"
   ansible.vm.network "private_network", ip: "192.168.10.9"
   ansible.vm.hostname = "ansible"
 end

 config.vm.define "java01" do |java01|
   java01.vm.network "private_network", ip: "10.100.0.131"
   java01.vm.hostname = "java01"
 end

end
