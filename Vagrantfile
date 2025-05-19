Vagrant.configure("2") do |config|
  # Base image
  config.vm.box = "debian/bookworm64"

  # VM definitions
  nodes = [
    { name: "jumpbox", ip: "192.168.56.5",  ram: 512 },
    { name: "server",  ip: "192.168.56.10", ram: 2048 },
    { name: "node-0",  ip: "192.168.56.11", ram: 2048 },
    { name: "node-1",  ip: "192.168.56.12", ram: 2048 },
  ]

  nodes.each do |node|
    config.vm.define node[:name] do |node_config|
      node_config.vm.hostname = node[:name]

      # Interface 1: NAT (for internet)
      # Automatically added by Vagrant (eth0)

      # Interface 2: Host-only/private network (for internal communication)
      node_config.vm.network "private_network", ip: node[:ip]

      # VM resources
      node_config.vm.provider "virtualbox" do |vb|
        vb.name = node[:name]
        vb.memory = node[:ram]
        vb.cpus = 1
      end

      # Root password setup and ssh config (no keys required)
      node_config.vm.provision "shell", inline: <<-SHELL
        echo -e "root\\nroot" | passwd root

        sed -i 's/^#*PermitRootLogin.*/PermitRootLogin yes/' /etc/ssh/sshd_config
        sed -i 's/^#*PasswordAuthentication.*/PasswordAuthentication yes/' /etc/ssh/sshd_config
        systemctl restart ssh

        # DNS fix (resolv.conf can be overwritten by DHCP, but good for now)
        echo 'nameserver 8.8.8.8' > /etc/resolv.conf
      SHELL
    end
  end
end

