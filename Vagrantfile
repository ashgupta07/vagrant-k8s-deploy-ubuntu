IMAGE_NAME = "peru/ubuntu-20.04-server-amd64"
N = 1

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false
    config.vm.network "private_network", type: "dhcp"
    config.vm.synced_folder ".", "/vagrant", disabled: true
    config.vm.provider "virtualbox" do |v|
        v.memory = 4096
        v.cpus = 2
        v.gui = false
    end
      
    config.vm.define "k8s-master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "192.168.50.10"
        master.vm.hostname = "k8s-master"
        master.vm.provision "shell", inline: <<-SHELL
          sudo apt-get update
          sudo apt-get install -y ansible
          sudo mkdir -p /vagrant
        SHELL
        master.vm.provision "file", source: "kubernetes-setup/master-playbook.yml", destination: "/tmp/master-playbook.yml"
        master.vm.provision "ansible_local" do |ansible|
            ansible.playbook = "/tmp/master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.50.10",
            }
        end
		master.vm.provision "shell", inline: <<-SHELL
          mkdir -p $HOME/.kube
		  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
		  sudo chown $(id -u):$(id -g) $HOME/.kube/config          
        SHELL
		config.trigger.after :up do |trigger|
		trigger.name = "create join command"
		trigger.run = {"inline": "vagrant scp k8s-master:/vagrant/join-command join-command"}
	    end
		config.trigger.after :up do |trigger|
		trigger.name = "create kubeconfig"
		trigger.run = {"inline": "vagrant scp k8s-master:/etc/kubernetes/admin.conf kubeconfig"}
	    end
    end
	
	

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "shell", inline: <<-SHELL
              sudo apt-get update
              sudo apt-get install -y ansible
              sudo mkdir -p /vagrant
            SHELL
            node.vm.provision "file", source: "kubernetes-setup/node-playbook.yml", destination: "/tmp/node-playbook.yml"
            node.vm.provision "file", source: "join-command", destination: "/tmp/join-command"
            node.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "/tmp/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{i + 10}",
                }
            end
        end
    end
end
