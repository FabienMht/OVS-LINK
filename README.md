# OVS-LINK
Performs integration of Open vSwitch with Docker/Kata-containers/Gvisor/Singularity/LXC/LXD.

**List of features and compatibles solutions :**

| Features/Solutions | Docker | Kata-containers | Gvisor | Singularity | LXC | LXD |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| Add int to ovs | Yes | No | No | Yes | Yes | - |
| Link int to ovs | No | Yes | Yes | No | No | - |
| Set VLAN | Yes | Yes | Yes | Yes | Yes | - |
| Set IP address | Yes | Yes | No | Yes | Yes | - |
| Set Gateway | Yes | Yes | No | Yes | Yes | - |
| Set DNS | Yes | Yes | No | Yes | Yes | - |
| DHCP | Yes | Yes | No | Yes | Yes | - |
| Limit ingress bandwith | Yes | Yes | Yes | Yes | Yes | - |
| Prevent ARP spoofing | Yes | Yes | Yes | Yes | Yes | - |
| Prevent IP spoofing | Yes | Yes | Yes | Yes | Yes | - |

**Capabilities needed :**
- Set IP address : cap_net_admin
- Set Gateway : cap_net_admin
- DHCP : cap_net_admin

**Solutions needed :**
- ALL : openvswitch, iproute2
- DHCP : dhclient in container
- ARP and IP spoofing : ebtables

**How to launch containers to be link to openvswitch :**
- Docker (runc)
  - `docker run -it --runtime=runc --net=none container`
- Kata-containers (kata-runtime)
  - `docker run -it --runtime=kata-runtime --cap-add=cap_net_admin container`
- Gvisor (runsc)
  - `docker run -it --runtime=runsc --cap-add=cap_net_admin container`
- Singularity
  - `singularity instance start --fakeroot -C --uts --net --network=none --writable container instance_name`
- Lxc
  - Fichier /var/lib/lxc/container/config : lxc.net.0.type = empty

# CLI

**Commands :**
```
add-port BRIDGE INTERFACE CONTAINER [--user=USER] [--ipaddress="ADDRESS"]
                  [--gateway=GATEWAY] [--dns=DNS] [--macaddress="MACADDRESS"]
                  [--mtu=MTU] [--dhcp] [--limitin=VALUE in Kb] [--arpguard]
                  [--ipguard]
set-port INTERFACE CONTAINER [--user=USER] [--ipaddress="ADDRESS"]
                  [--gateway=GATEWAY] [--dns=DNS] [--macaddress="MACADDRESS"]
                  [--mtu=MTU] [--dhcp] [--limitin=VALUE in Kb] [--arpguard=yes/no]
                  [--ipguard=yes/no] [--vlan=VLAN/no]
del-port BRIDGE INTERFACE CONTAINER [USER]
del-ports BRIDGE CONTAINER [USER]
flush-ports BRIDGE
```

**Add an interface to ovs bridge :**  
`sudo ovs-link add-port bridge interface container`

**Add an interface to ovs bridge with ip address and gateway :**  
`sudo ovs-link add-port bridge interface container --ipaddress="192.168.50.6/24" --gateway=192.168.50.1 --dns=8.8.8.8`

**Add an interface to ovs bridge with arp and ip spoofing prevention and bandwith limit :**  
`sudo ovs-link add-port bridge interface container --limitin=1000000 --arpguard --ipguard`

**Deletes ports where the interface on the host is deleted :**  
`sudo ovs-link flush-ports bridge`