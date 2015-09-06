Vagrant.configure(2) do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = false
  config.vm.hostname = "fileapi.dev"
  config.vm.network "private_network", ip: "192.168.20.10"

  # Folder shares.
  # Use NFS for Puppet, because it does not need a specific owner.
  # Use rsync for the application, because those folders need to be owned by the 'fileapi'
  # user, and NFS does not support changing the owner of a synced folder.
  config.vm.synced_folder "./site", "/home/fileapi/project/site/current",
    type: "rsync",
    create: true,
    owner: "fileapi",
    group: "fileapi",
    rsync__auto: true,
    rsync__exclude: ["vagrant2015*", "d2015*"]

  config.vm.synced_folder "./api", "/home/fileapi/project/api/current",
    type: "rsync",
    create: true,
    owner: "fileapi",
    group: "fileapi",
    rsync__auto: true,
    rsync__exclude: ["var/", "vagrant2015*", "d2015*"]

  config.vm.synced_folder "./puppet", "/root/puppet",
    type: "nfs"

  # Provider-specific configuration.
  config.vm.provider "virtualbox" do |vb|
    vb.name = "fileapi_dev"
  end

  config.vm.provision "shell", inline: <<-SHELL
    echo 'Setting up system dependencies.'
    # nginx (the default version in the Ubuntu PPA is very old and doesn't support add_header on non-200 status responses).
    add-apt-repository -y ppa:nginx/development
    # ffmpeg (replaced by libav in the default Ubuntu PPA):
    add-apt-repository -y ppa:mc3man/trusty-media
    apt-get update
    [ -f /usr/bin/git ] || apt-get install git -y
    [ -f /usr/bin/puppet ] || apt-get install puppet -y

    echo 'Setting up Node dependencies that are not yet install via Puppet.'
    apt-get install npm -y
    npm install -g grunt-cli
    ln -s /usr/bin/nodejs /usr/bin/node

    echo 'Starting Puppet.'
    cd /root/puppet
    git checkout master
    ./papply

    echo 'Stopping Puppet agent because the machine has been provisioned via Puppet apply.'
    service puppet stop

    echo 'Fixing permissions.'
    chown -R fileapi:fileapi /home/fileapi/project

    echo 'Finished.'
  SHELL

  config.vm.post_up_message = <<-MESSAGE
    Your FileAPI.net development VM has been created!

    You can SSH into the VM with `vagrant ssh`

    In your SSH session, to run commands as root, your personal user account should be able to
    run sudo commands without a password prompt. To run application commands, make sure you run them
    as the 'fileapi' user (`sudo su - fileapi` to become that user).

    If everything else is set up correctly, you can see the website in your browser at:
    http://fileapi.dev/

    Now start syncing the API and site folders: `vagrant rsync-auto`
  MESSAGE
end
