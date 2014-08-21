# Vagrant.require_plugin "vagrant-esxi"


File.open("version.txt", 'w') do |f|
  f.write(Vagrant::VERSION)
end

Vagrant.configure("2") do |config|
  config.ssh.default.username = "root"
  config.ssh.shell = "sh"

  config.vm.box = "vmware_esxi55"
  config.vm.hostname = "vagrantbox"
  config.vm.synced_folder ".", "/vagrant", nfs: true

  [:vmware_workstation, :vmware_fusion].each do |name|
    config.vm.provider name do |v,override|
      
      config.vm.provision "shell", inline: "echo Using VMware provisioner"
      config.vm.box_url = "http://s3.amazonaws.com/pse-boxes/vmware_esxi55.box"
      v.gui = true
      v.vmx["memsize"] = "12288"
      v.vmx["numvcpus"] = "4"
    end
  end
    
#  [:virtualbox].each do |name|
#    config.vm.provider name do |v,override|
#      config.vm.provision "shell", inline: "echo Using VirtualBox provisioner"
#      config.vm.box = "virtualbox_esxi55.box"
#      config.vm.box_url = "http://s3.amazonaws.com/pse-boxes/virtualbox_esxi55.box"
#      v.gui = true
#     v.vmx["memsize"] = "8192"
#     v.vmx["numvcpus"] = "4"
#    end  
#  end

  config.vm.provision "shell", privileged: false, path: "provision.sh"
  config.vm.provision "shell", inline: "echo Configuration complete"
end
