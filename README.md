macOS ansible host

vagrant box add jborean93/WindowsServer2019 --provider virtualbox

brew install ansible

brew install pipx

pipx inject ansible pywinrm  # if you installed ansible with pipx

pipx inject ansible-core pywinrm  # if you installed ansible-core with pipx

This ansible playbook for Windows Operating System and has been tested on Vagrant Windows2019 successfully.

Vagrant will create two Windows 2019 machines. One of them will be configured as a Windows Domain Controller and another just a standard Windows 2019 without even join the domain yet. The aim of this exercise to see we can create a Windows Domain Group and/or user remotely by running ansible point to a standard Windows 2019, but **delegate_to** to Windows Domain Controller and see if a Windows Domain Group get created. Also, user get created too. Each user will assign to some groups.

Paid attention to **inventory.yml** file and group_vars/all/main.yml

This inventory YAML file has configure as per Ansible documentation of using hosts. For example

The main gotcha is that the Windows native is winrm connection. Therefore, we need to create two set of machines in the Vagrantfile and do port_forwarded to each on different ports as follows:-

<table>
<tr>
<td>
windows2019_ad machine with some important details from Vagrantfile

win_ad.vm.communicator = "winrm"
win_ad.winrm.guest_port = "5986"
win_ad.winrm.port = "55988"
win_ad.winrm.transport = :ssl
win_ad.winrm.ssl_peer_verification = false

win_ad.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", disabled: true
win_ad.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
win_ad.vm.network :forwarded_port, guest: 5985, host: 55987, id: "winrm", auto_correct: true
win_ad.vm.network :forwarded_port, guest: 5986, host: 55988, id: "winrm_ssl", auto_correct: true

win_ad.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "setup.yml"
    ansible.inventory_path = "inventory.yml"
    ansible.verbose = "-vvv"
end
</td>
<td>
windows2019 - A dummy machine to see Ansible can be run and create Windows Domain Group and User remotely by relying on this machine without a to run on the Windows Domain Controller.

win.vm.communicator = "winrm"
win.winrm.guest_port = "5986"
win.winrm.port = "55991"
win.winrm.transport = :ssl
win.winrm.ssl_peer_verification = false

win.vm.network :forwarded_port, guest: 22, host: 2223, id: "ssh", disabled: true
win.vm.network :forwarded_port, guest: 3389, host: 3390, id: "rdp", auto_correct: true
win.vm.network :forwarded_port, guest: 5985, host: 55990, id: "winrm", auto_correct: true
win.vm.network :forwarded_port, guest: 5986, host: 55991, id: "winrm_ssl", auto_correct: true

win.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "site.yml"
    ansible.inventory_path = "inventory.yml"
    ansible.verbose = "-vvv"
end
</td>
</tr>
</table>

As you can see that each machine winrm port and guest_port is different, including ssh or RDP. Pay attention to the Vagrantfile and inventory.yml in case you want to say RDP to 3389 and 3390 as both can not have the same port.