## Creating an interfacce
```
sudo ip tuntap add user $(whoami) mode tun ligolo
```
```
sudo ip link set ligolo up
```
```
sudo ip route add 192.168.1.0/24 dev ligolo
```