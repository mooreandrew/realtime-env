Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"

  config.ssh.forward_agent = true

  config.vm.synced_folder "./apps", "/home/vagrant/apps"


  config.vm.network :forwarded_port, host: 4567, guest: 4567
  config.vm.network :forwarded_port, host: 8080, guest: 8080
  config.vm.network :forwarded_port, host: 5432, guest: 5432

    # Windows github ssh keys fix
  if Vagrant::Util::Platform.windows?
    # You MUST have a ~/.ssh/github_rsa (GitHub specific) SSH key to copy to VM
    if File.exists?(File.join(Dir.home, ".ssh", "github_rsa"))
      # Read local machine's GitHub SSH Key (~/.ssh/github_rsa)
      github_ssh_key = File.read(File.join(Dir.home, ".ssh", "github_rsa"))
      # Copy it to VM as the /root/.ssh/id_rsa key
      config.vm.provision :shell, :inline => "echo 'Windows-specific: Copying local GitHub SSH Key to VM for provisioning...' && mkdir -p /root/.ssh && echo '#{github_ssh_key}' > /root/.ssh/id_rsa && chmod 600 /root/.ssh/id_rsa"
      config.vm.provision :shell, :inline => "echo 'Windows-specific: Copying local GitHub SSH Key to VM for provisioning...' && mkdir -p /home/vagrant/.ssh && echo '#{github_ssh_key}' > /home/vagrant/.ssh/id_rsa && chmod 600 /home/vagrant/.ssh/id_rsa"
    else
      # Else, throw a Vagrant Error. Cannot successfully startup on Windows without a GitHub SSH Key!
      raise Vagrant::Errors::VagrantError, "\n\nERROR: GitHub SSH Key not found at ~/.ssh/github_rsa (required on Windows).\nYou can generate this key manually OR by installing GitHub for Windows (http://windows.github.com/)\n\n"
    end
 end

  config.vm.provider :virtualbox do |vb|
    vb.customize ['modifyvm', :id, '--memory', ENV['VM_MEMORY'] || 4096]
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
    vb.customize ['modifyvm', :id, '--natdnsproxy1', 'on']
    vb.customize ["modifyvm", :id, "--cpus", ENV['VM_CPUS'] || 4]
  end

  config.vm.provision :shell, :path => 'provision'

end
