# -*- mode: ruby -*-
# vi: set ft=ruby :

# -*- mode: ruby -*-
# vi: set ft=ruby :

# $basic_auth = <<SCRIPT
# Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
# SCRIPT

# $allow_execution_on_powershell = <<SCRIPT
# Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force
# SCRIPT

# $ansible_remoting_script = <<SCRIPT
# iwr vagrant/ConfigureRemotingForAnsible.ps1 -UseBasicParsing | iex
# SCRIPT

# $ansible_remoting_script = <<SCRIPT
# iex ((new-object net.webclient).DownloadString('https://raw.githubusercontent.com/ansible/ansible-documentation/devel/examples/scripts/ConfigureRemotingForAnsible.ps1'))
# SCRIPT

Vagrant.require_version ">= 1.6.2"

Vagrant.configure("2") do |config|
  config.vm.define "windows2019_ad" do |win_ad|
    win_ad.vm.box = "jborean93/WindowsServer2019"
    win_ad.vm.communicator = "winrm"
    win_ad.winrm.guest_port = "5986"
    win_ad.winrm.port = "55988"
    win_ad.winrm.transport = :ssl
    win_ad.winrm.ssl_peer_verification = false

    # Admin user name and password
    win_ad.winrm.username = "vagrant"
    win_ad.winrm.password = "vagrant"

    win_ad.vm.guest = :windows
    win_ad.windows.halt_timeout = 60
    win_ad.vm.boot_timeout = 900 # Give sysprep first-boot enough time
    win_ad.vm.graceful_halt_timeout = 900 # Give windows update time

    win_ad.vm.network "private_network", ip: "192.168.56.181"
    # config.vm.provision "shell", privileged: "true", powershell_elevated_interactive: "true", inline: <<-SHELL
    #   Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
    #   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force
    # SHELL
    #config.vm.provision "shell", path: "vagrant/ConfigureRemotingForAnsible.ps1", privileged: true, powershell_elevated_interactive: "true"

    win_ad.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", disabled: true
    win_ad.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
    win_ad.vm.network :forwarded_port, guest: 5985, host: 55987, id: "winrm", auto_correct: true
    win_ad.vm.network :forwarded_port, guest: 5986, host: 55988, id: "winrm_ssl", auto_correct: true
  #config.vm.synced_folder "./vagrant", "C:\\vagrant\\vagrant"

    win_ad.vm.provider :virtualbox do |v, override|
      v.gui = true
      v.customize ["modifyvm", :id, "--memory", 8192]
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--vram", 128]
      v.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]

    #   file_to_disk = File.realpath( "." ).to_s + "/disk.vdi"
    #   if ARGV[0] == "up" && ! File.exist?(file_to_disk)
    #     v.customize [
    #       'createhd',
    #       '--filename', file_to_disk,
    #       '--format', 'VDI',
    #       '--size', 30 * 1024 # 30 GB
    #     ]
    #     v.customize [
    #       'storageattach', :id,
    #       '--storagectl', 'SATA', # The name may vary
    #       '--port', 1, '--device', 0,
    #       '--type', 'hdd', '--medium',
    #       file_to_disk
    #     ]
    #   end
      
    #   filetodisk = File.realpath( "." ).to_s + "/disk1.vdi"
    #   if ARGV[0] == "up" && ! File.exist?(filetodisk)
    #     v.customize [
    #       'createhd',
    #       '--filename', filetodisk,
    #       '--format', 'VDI',
    #       '--size', 30 * 1024 # 30 GB
    #     ]
    #     v.customize [
    #       'storageattach', :id,
    #       '--storagectl', 'SATA', # The name may vary
    #       '--port', 2, '--device', 0,
    #       '--type', 'hdd', '--medium',
    #       filetodisk
    #     ]
    #   end
    end

    win_ad.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "setup.yml"
      ansible.inventory_path = "inventory.yml"
      ansible.verbose = "-vvv"
    end
  end

  config.vm.define "windows2019" do |win|
    win.vm.box = "jborean93/WindowsServer2019"
    win.vm.communicator = "winrm"
    win.winrm.guest_port = "5986"
    win.winrm.port = "55991"
    win.winrm.transport = :ssl
    win.winrm.ssl_peer_verification = false

    # Admin user name and password
    win.winrm.username = "vagrant"
    win.winrm.password = "vagrant"

    win.vm.guest = :windows
    win.windows.halt_timeout = 60
    win.vm.boot_timeout = 900 # Give sysprep first-boot enough time
    win.vm.graceful_halt_timeout = 900 # Give windows update time

    win.vm.network "private_network", ip: "192.168.56.182"
    # config.vm.provision "shell", privileged: "true", powershell_elevated_interactive: "true", inline: <<-SHELL
    #   Set-Item -Path WSMan:\localhost\Service\Auth\Basic -Value $true
    #   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force
    # SHELL
    #config.vm.provision "shell", path: "vagrant/ConfigureRemotingForAnsible.ps1", privileged: true, powershell_elevated_interactive: "true"

    win.vm.network :forwarded_port, guest: 22, host: 2223, id: "ssh", disabled: true
    win.vm.network :forwarded_port, guest: 3389, host: 3390, id: "rdp", auto_correct: true
    win.vm.network :forwarded_port, guest: 5985, host: 55990, id: "winrm", auto_correct: true
    win.vm.network :forwarded_port, guest: 5986, host: 55991, id: "winrm_ssl", auto_correct: true
  #config.vm.synced_folder "./vagrant", "C:\\vagrant\\vagrant"

    win.vm.provider :virtualbox do |v, override|
      v.gui = true
      v.customize ["modifyvm", :id, "--memory", 8192]
      v.customize ["modifyvm", :id, "--cpus", 2]
      v.customize ["modifyvm", :id, "--vram", 128]
      v.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]

    #   file_to_disk = File.realpath( "." ).to_s + "/disk.vdi"
    #   if ARGV[0] == "up" && ! File.exist?(file_to_disk)
    #     v.customize [
    #       'createhd',
    #       '--filename', file_to_disk,
    #       '--format', 'VDI',
    #       '--size', 30 * 1024 # 30 GB
    #     ]
    #     v.customize [
    #       'storageattach', :id,
    #       '--storagectl', 'SATA', # The name may vary
    #       '--port', 1, '--device', 0,
    #       '--type', 'hdd', '--medium',
    #       file_to_disk
    #     ]
    #   end
      
    #   filetodisk = File.realpath( "." ).to_s + "/disk1.vdi"
    #   if ARGV[0] == "up" && ! File.exist?(filetodisk)
    #     v.customize [
    #       'createhd',
    #       '--filename', filetodisk,
    #       '--format', 'VDI',
    #       '--size', 30 * 1024 # 30 GB
    #     ]
    #     v.customize [
    #       'storageattach', :id,
    #       '--storagectl', 'SATA', # The name may vary
    #       '--port', 2, '--device', 0,
    #       '--type', 'hdd', '--medium',
    #       filetodisk
    #     ]
    #   end
    end

    win.vm.provision "ansible" do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "site.yml"
      ansible.inventory_path = "inventory.yml"
      ansible.verbose = "-vvv"
    end
  end
end