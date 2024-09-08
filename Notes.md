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
- repeat for `node0`, `node1`, `node2`, `node3`

