Assuming you have this directory structure of the FileAPI.net repositories:

    ./Vagrantfile
    ./api/
    ./site/
    ./puppet/

First, in the Puppet repository, install our dependencies with `git submodule update --init --recursive`.

Then, run `vagrant up`. This will provision a Vagrant box which you can SSH to via `vagrant ssh`.

Add to your `/etc/hosts`:

    192.168.20.10  api.fileapi.dev demo-api.fileapi.dev
    192.168.20.10  fileapi.dev www.fileapi.dev
    192.168.20.10  files.fileapi.dev

When your Vagrant box is running, the site will be accessible at `http://fileapi.dev/`

To sync your API and site folders, run `vagrant rsync-auto`
