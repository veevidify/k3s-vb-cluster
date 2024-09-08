# k3s

# start virtualbox VMs
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

