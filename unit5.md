PART 2

The rules that need is that the first machine can ssh into the second, but the second cannot into the first.

In this case:

192.168.56.100 can ssh into 192.168.56.101
192.168.56.101 can not ssh into 192.168.56.100

To do this, we need to let 192.168.56.100 accept all incoming connections as default, and specifcally deny ssh requests from 192.168.56.101

The first command will be executed on machine 1 which blocks ssh requests specifically from 192.168.56.101, however will not stop other forms of requests like pings from being executed 

sudo iptables -A INPUT -p tcp --dport ssh -s 192.168.56.101 -j REJECT

Here is a break down of the command:
-A: appends rules to the iptables
INPUT: specifcies a connection 
-p tcp: denotes whcih protocol is being used 
ssh: defines what protocol we are executing on/targeting
REJECT: blocks the connection specified and returns an error message


PART 3

Using port 12345 with ssh connects to port 22 on the second virtual machine (192.168.56.101). This means when .101 tries to ssh to .100, the connection is reirected to port 22 on the .101 machine

To do this, ip forwarding needs to be enabled

sudo sysctl net.ipv4.ip_forward=1

The following commands can then be used to redirect to port 22

sudo iptables -t nat -A PREROUTING -p tcp --dport 12345 -j DNAT --to-destination 192.168.56.101:22

and then 

sudo iptables -t nat -A POSTROUTING -j MASQUERADE

When machine 2 (.101) now tries to ssh into machine 1 (.100) through 12345, it will be re directed to port 22 
