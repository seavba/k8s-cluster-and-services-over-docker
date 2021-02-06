IMAGE_NAME = "ubuntu/focal64"
N = 2

Vagrant.require_version ">= 2.2.7"
Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |vbox|
    vbox.memory = 1024
    vbox.cpus = 1
  end

#=begin
  config.vm.define "master" do |master|
    master.vm.box = IMAGE_NAME
    master.vm.hostname = "master"
    master.vm.provider :virtualbox do |vb|
          vb.customize ["modifyvm", :id, "--memory", "8192"]
          vb.customize ["modifyvm", :id, "--cpus", "4"]
      end
    config.vm.network "private_network", ip: "172.22.0.10"
    master.vm.network "private_network", ip: "192.168.100.10",
      virtualbox__intnet: "k8snet"

    master.vm.provision "master", type: "ansible" do |ansible|
      ansible.playbook = "kubernetes-setup/playbooks/k8sMaster-main.yml"
      ansible.compatibility_mode = "2.0"
      ansible.extra_vars = {
        node_ip: "192.168.100.10",
        cidr: "192.168.100.0/24",
      }
    end
  end

#=end
  (1..N).each do |i|
    config.vm.define "node-#{i}" do |node|
      node.vm.box = IMAGE_NAME
      node.vm.network "private_network", ip: "192.168.100.#{i + 10}",
        virtualbox__intnet: "k8snet"
      node.vm.hostname = "node-#{i}"
      node.vm.provision "node-#{i}", before: "redis", type: "ansible" do |ansible|
        ansible.playbook = "kubernetes-setup/playbooks/k8sNode-main.yml"
        ansible.compatibility_mode = "2.0"
        ansible.extra_vars = {
            node_ip: "192.168.100.#{i + 10}",
        }
      end
    end
  end
end
