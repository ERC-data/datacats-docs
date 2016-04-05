System setup
============

The SA Energy Research Database is hosted on a virtual server provided by UCT's eResearch team.
Contact person: Ashley Rustin
Log on to server with ``ssh user@xxx.xxx.xxx.xx`` .
Make sure you're operating in your root directory ``(/)`` not home directory ``(~)`` when working on the virtual machine.

Server configuration
********************
Set up your server according to https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-14-04.
Also follow additional recommended server setup steps: https://www.digitalocean.com/community/tutorials/additional-recommended-steps-for-new-ubuntu-14-04-servers

Git details
***********
::

  git config user.name "SAEnergyData"
  git config user.email "*****"

To pull plugins from github, a public key must be added and registered in your github account. Read about it here: https://help.github.com/articles/generating-a-new-ssh-key/#platform-linux .
To use xclip on remote server log in to ssh session with ``-X` option

Add a local repository to GitHub
********************************
Full instructions available online https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/#platform-linux::

  #cd local directory
  git init
  git add .
  git commit -m "First commit"
  git remote add origin remote-repository-URL #use 'upstream' instead of 'origin' when not own repo
  git push origin master

Sync a fork with its upstream repository
****************************************
::

  #cd local directory
  git fetch upstream
  git checkout master
  git merge upstream/master

Firewall
********
Configure your firewall / ufw settings::

  sudo ufw enable
  sudo ufw allow ssh, 22, 2375/tcp, 80, 5000, 8800, 587

Detailed configuration settings for your firewall / ufw are available at https://help.ubuntu.com/community/UFW.

Folder structure
*****************
::

  sudo mkdir /var/venvs #folder for virtual environments
  sudo mkdir /var/projects #projects folder
  chown -R user:www-data /var/venvs /var/projects #change folder ownership to grant the webserver access

Virtual environment
*******************
Install virtualenv and virtualenvwrapper::

  sudo pip install virtualenv 
  sudo pip install virtualenvwrapper

Customise `~/.bashrc` to enable virtualenvwrapper on startup::

  sudo nano ~/.bashrc 
  export WORKON_HOME=/var/venvs
  export PROJECT_HOME=/var/projects 
  source /usr/local/bin/virtualenvwrapper.sh
  source ~/.bashrc

Create virtualenv with virtualenvwrapper::

  mkproject ckan

Install Docker from source
**************************
Follow all Docker install steps from https://docs.docker.com/engine/installation/linux/ubuntulinux/. The Docker port is 2375 and must be enabled. This should have been done during server configuration. DON'T INSTALL DOCKER WITH APT-GET as this Docker version is outdated and does not allow for a successful datacats install. Instead download the most recent version directly from Docker::

  sudo apt-get update
  wget -qO- https://get.docker.com/ | sh
  sudo service docker start
  sudo docker run hello-world #test
  sudo usermod -aG docker username #create docker group & user | log out and log in again to activate
  docker run hello-world #test that Docker works without sudo

To learn more about Docker watch the introduction training video: https://training.docker.com/self-paced-training
