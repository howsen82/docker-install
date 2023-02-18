# Docker Desktop / Docker Engine Installation Manual

# Installation on Windows
---

The installation is usually absolutely uncomplicated. You need to install WSL2 for Windows Subsystems, WSL2 get activated automatically once the installation process has completed.

You can download the latest version of Docker Desktop here:

<code>http://docs.docker.com/docker-for-windows/install</code>

To check for the docker version

<pre><code> > docker version

Client: Docker Engine - Community
 Version:           23.0.1
 API version:       1.42
 Go version:        go1.19.5
 Git commit:        a5ee5b1
 Built:             Thu Feb  9 19:47:01 2023
 OS/Arch:           linux/amd64
 Context:           default

Server: Docker Engine - Community
 Engine:
  Version:          23.0.1
  API version:      1.42 (minimum version 1.12)
  Go version:       go1.19.5
  Git commit:       bc3805a
  Built:            Thu Feb  9 19:47:01 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.18
  GitCommit:        2456e983eb9e37e47538f59ea18f2043c9a73640
 runc:
  Version:          1.1.4
  GitCommit:        v1.1.4-0-g5fd4c4d
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
</code></pre>

---
# Installation on macOS
To download the correct DMG file version that matches your CPU architecture from the following website

<code>[Apple macOS](https://docs.docker.com/desktop/install/mac-install/)</code>

---
# Installation on Linux

**Pre-requisites**
1. Create a docker group
<code>sudo groupadd docker</code>
2. Add your user to the docker group
<code>sudo usermod -aG docker $USER</code>

<ins>**Ubuntu**</ins>
Remove previously installed packages from the Ubuntu package

<pre><code>sudo apt remove docker docker-engine docker.io containerd runc</code></pre>

Next, you need to install a few base packages

<pre><code>sudo apt install apt-transport-https ca-certificates curl gnupg lsb_release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre>

If you have use the Uncomplicated Firewall (UFW) on Ubuntu. Docker containers can't use a network bridge. The culprit is the default settings of UFW, which prevents IP packets from being transferred. A solution to this could be to make change in */etc/default/ufw*, and then restart the firewall via
<pre><code> > sudo ufw reload

# File /etc/default/ufw
...
DEFAULT_FORWARD_POLICY="ACCEPT"</code></pre>

<ins>**Debian**</ins>
The installation process on Debina is similar to Ubuntu; the only difference is the name of the package source.

<code>[Source](https://docs.docker.com/engine/install/debian)</code>

Remove previously installed packages from the Ubuntu package
<pre>
<code>sudo apt-get remove docker docker-engine docker.io containerd runc</code>
</pre>

Next, you need to install a few base packages
<pre><code>sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg lsb_release
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre>

<ins>**Raspberry PI**</ins>
The bestway to proceed is to use a script provided by Docker
<pre><code>curl -fsSL https://get.docker.com -o get-docker.sh
less get-docker.sh
sudo sh get-docker.sh</code></pre>

<ins>**Red Hat Enterprise Linux 8 / Fedora**</ins>
Before you begin, you should make sure Podman and its container build systen Buildah are removed:

<pre><code>sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine podman runc buildah</code></pre>

Then you need to setup new package source and install Docker using *yum-config-manager* from the *yum-util* package. The following commands are valid for RHEL:

<pre><code>dnf install dnf-plugins-core
sudo yum install -y yum-utils
sudo yum-config-manager -add-repo https://download.docker.com/linux/rhel/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre>

<ins>**CentOS**</ins>
Remove previous installed packages
<pre><code>sudo yum remove docker docker-client docker-client-latest docker-common docker-latest docker-latest-logrotate docker-logrotate docker-engine podman runc buildah</code></pre>

Then follow the commands:

<pre><code>sudo yum install -y yum-utils
sudo yum-config-manager -add-repo https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin</code></pre>

For others variants installation, please refer to [here](https://docs.docker.com/engine/install).

<h3>Troubleshooting / Logging</h3>
To test whether Docker is running, you must execute the <code>docker version</code> command. Depending on the kernel version, Docker deamon may provide warning that certain features aren't available:

<pre><code>> journalctl -u docker | grep warning

warning: Your kernel does not support CPU realtime scheduler
warning: Your kernel does not support cgroup blkio weight
warning: Your kernel does not support cgroup blkio weight_device</code></pre>

<ins>**Rootless Docker**</ins>
To get linux unique ID utils, install the command as follow
<pre><code>apt install uidmap           # Debian
dnf install shadow-utils     # Fedora/RHEL</code></pre>

If previous docker being installed, disable its service first

<pre><code>sudo systemctl disable --now docker
sudo systemctl disable --now docker.service docker.socket</code></pre>

**Installation**

<pre><code>sudo apt install -y dbus-user-session
sudo apt install -y docker-ce-rootless-extras
dockerd-rootless-setuptool.sh install
systemctl --user start docker
systemctl --user enable docker
sudo loginctl enable-linger $(whoami)
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock</code></pre>
For these settings to take effect, you must log out and log in again. Afterward, run <code>docker version</code> should resturn the usual result.

If you haven't installed docker engine before, you can run command as follow:
<pre><code>curl -fsSL https://get.docker.com/rootless | sh
export DOCKER_HOST=unix://$XDG_RUNTIME_DIR/docker.sock</code></pre>

**Uninstall**

<pre><code>dockerd-rootless-setuptool.sh uninstall
cd ~/bin
rm -f containerd containerd-shim containerd-shim-runc-v2 ctr docker docker-init docker-proxy dockerd dockerd-rootless-setuptool.sh dockerd-rootless.sh rootlesskit rootlesskit-docker-proxy runc vpnkit
sudo systemctl enable --now docker</code></pre>

For other variants installation, please refer [here](https://docs.docker.com/engine/security/rootless/)

