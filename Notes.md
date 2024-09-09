# k3s
## some prereq
- apart from getting `vagrant`, `virtualbox` up & working
- make sure to have installed:
```sh
sudo apt-get --no-install-recommends install virtualbox-guest-utils
vagrant plugin install vagrant-vbguest
vagrant plugin update
```

## start virtualbox VMs
- start the VMs
```sh
cd bootstrap/iac
vagrant up
```
- initial launch, fix vbox:
```sh
vagrant ssh server
# then
sudo rcvboxadd reload
```
- or from host, whenever box init is stuck:
```sh
./fix-startup.sh server
./fix-startup.sh node0
```
- repeat for `node0`, `node1`, `node2`

## provision the server
### install k3s for controlplane
- install using rancher's provided script
```sh
# host
vagrant ssh server
```
```sh
# vagrant@server:~$

# as root
sudo -i

# first check our network interface:
ip address # 192.168.56.100 -> eth1
export SERVER_IP=192.168.56.100

# set k3s exec args
export INSTALL_K3S_EXEC="--bind-address=$SERVER_IP --node-external-ip=$SERVER_IP --flannel-iface=eth1"

# pull script & install
curl -sfL https://get.k3s.io | sh -

# check
systemctl status k3s

# share token & kind: Config with nodes
# via Vagrant shared folder
cp /var/lib/rancher/k3s/server/token /vagrant_shared
cp /etc/rancher/k3s/k3s.yaml /vagrant_shared
```
- check
```sh
kubectl get nodes
kubectl get services
```
- rewrite `Vagrantfile`
```ruby
server_provision = <<-SHELL
    export SERVER_IP=192.168.56.100
    export INSTALL_K3S_EXEC="--bind-address=$SERVER_IP --node-external-ip=$SERVER_IP --flannel-iface=eth1"
    curl -sfL https://get.k3s.io | sh -
    echo "let k3s start ..."
    sleep 10
    cp /var/lib/rancher/k3s/server/token /vagrant_shared
    cp /etc/rancher/k3s/k3s.yaml /vagrant_shared
SHELL
```

## provision the worker
### install k3s for worker node
- with token & `K3S_URL` provided, installation will recognise we're working on worker node instead of controlplane
```sh
# host
vagrant ssh node0
```
```sh
# vagrant@node0:~$

# as root
sudo -i
```
- rewrite `Vagrantfile`

