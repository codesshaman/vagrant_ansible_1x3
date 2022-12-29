# -*- mode: ruby -*-
# vi: set ft=ruby :

MASTER_CPU = "2"
MASTER_MEMORY = "2048"

SLAVE_CPU = "1"
SLAVE_MEMORY = "1024"

# create machines config
Vagrant.configure(2) do |config|
    config.vm.box = "bento/debian-11.5"
    config.vm.provider "virtualbox" do |v|
    config.vm.synced_folder ".", "/mnt", type: "virtualbox"
        # for connect with SSH on both machines with no password
        id_rsa_pub = File.read("#{Dir.home}/.ssh/id_rsa.pub")
        config.vm.provision "copy ssh public key", type: "shell",
        inline: "echo \"#{id_rsa_pub}\" >> /home/vagrant/.ssh/authorized_keys"
    end

    WorkerCount = 3
    WORKERS_IP="192.168.58.91\
                192.168.58.92\
                192.168.58.93"
  # worker node config
  (1..WorkerCount).each do |i|
    config.vm.define "worker#{i}" do |worker|
        worker.vm.hostname = "worker#{i}"
        worker.vm.network :private_network, ip: "192.168.58.9#{i}"
        # run script for worker node with arguments
        worker.vm.provision "shell", privileged: true,
        path: "slave_node_setup.sh",
        args: [MASTER_NODE_IP, "192.168.58.9#{i}"]
        worker.vm.network "forwarded_port",
        guest: 9100, host: 9100
        worker.vm.provider "virtualbox" do |v|
            v.name = "puppet_#{i}"
            v.memory = SLAVE_MEMORY
            v.cpus = SLAVE_CPU
        end
    end
end

    MasterCount = 1

    # master node config
    (1..MasterCount).each do |i|
    MASTER_NODE_IP = "192.168.58.10#{i}"
    config.vm.define "master#{i}" do |master|
        master.vm.hostname = "master#{i}"
        master.vm.network :private_network, ip: MASTER_NODE_IP
        # run script for master node with argument
        master.vm.provision "shell", privileged: true,
        path: "master_node_setup.sh",
        args: [MASTER_NODE_IP, WORKERS_IP]
        master.vm.network "forwarded_port",
        guest: 9100, host: 9100
        master.vm.provider "virtualbox" do |v|
            v.name = "master_of_puppets_#{i}"
            v.memory = MASTER_MEMORY
            v.cpus = MASTER_CPU
        end
    end
end

end
