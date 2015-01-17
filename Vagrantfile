VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |vagrant|

  # VMware Configuration
  vagrant.vm.provider "vmware_fusion" do |provider, override|
    override.vm.box = "vmware-trusty64-20150105.box"
    override.vm.box_url = 'https://s3-us-west-2.amazonaws.com/technolo-g/vagrant-boxes/ubuntu/vmware-trusty64-20150105.box'
  end
  vagrant.vm.provider "vmware_fusion" do |v|
    v.vmx["memsize"] = '1024'
    v.vmx["numvcpus"] = '1'
  end

  # VirtualBox Configuration
  vagrant.vm.provider "virtualbox" do |provider, override|
    override.vm.box = "vbox-trusty64-20150111.box"
    override.vm.box_url = 'https://s3-us-west-2.amazonaws.com/technolo-g/vagrant-boxes/ubuntu/vbox-trusty64-20150111.box'
  end
  vagrant.vm.provider "virtualbox" do |v|
    v.memory = 1024
    v.cpus = 1
  end

  # Machine Configuration
  # Docker Hosts
  (1..3).each do |i|
    vagrant.vm.define "dockerhost0#{i}" do |config|
      config.vm.hostname = "dockerhost0#{i}"
      config.vm.network "private_network", ip: "10.100.199.20#{i}"
      config.vm.provision :ansible do |ansible|
        ansible.playbook = 'ansible/vagrant_docker_host.yml'
        ansible.groups   = {'vagrant_dockerhosts' => ["dockerhost0#{i}"], 'local' => ['localhost']}
        ansible.raw_arguments = '--timeout=30'
        ansible.host_key_checking = false
      end
    end
  end

  # Swarm Host
  vagrant.vm.define "dockerswarm01" do |config|
    config.vm.hostname = "dockerswarm01"
    config.vm.network "private_network", ip: "10.100.199.200"
    config.vm.network "forwarded_port", guest: 2376, host: 2376
    config.vm.network "forwarded_port", guest: 2375, host: 2375
    config.vm.provision :hosts
    config.vm.provision :ansible do |ansible|
      ansible.playbook = 'ansible/vagrant_docker_swarm.yml'
      ansible.groups   = {'vagrant_dockerswarm' => ["dockerswarm01"], 'local' => ['localhost']}
      ansible.raw_arguments = '--timeout=30'
      ansible.host_key_checking = false
    end
  end

  vagrant.vm.define "dockerproxy01" do |config|
    config.vm.hostname = "dockerproxy01"
    config.vm.network "private_network", ip: "10.100.199.31"
    config.vm.provision :hosts
    config.vm.provision :ansible do |ansible|
      ansible.playbook = 'ansible/vagrant_docker_proxy.yml'
      ansible.groups   = {'vagrant_dockerproxy' => ["dockerproxy01"], 'local' => ['localhost']}
      ansible.raw_arguments = '--timeout=30'
      ansible.host_key_checking = false
    end
  end

  # Make sure all hosts have all entries
  (1..3).each do |i|
    vagrant.vm.define "dockerhost0#{i}" do |config|
      config.vm.provision :hosts
    end
  end
end
