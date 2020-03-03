# geoblock-ipset-generator


- check if IPSET is installed
- readme instructions
- test on different versions of python 2.7
- test installation 2.7 
- test download 2.7

add ipset list test | less to the end

https://lite.ip2location.com/file-download


On the PREROUTING chain (could cause issues with network namespaces and virtualisations)

On the INPUT chain (lower performance than PREROUTING but less likely to cause side effects)
WHITELIST
iptables -I INPUT -p tcp -j REJECT
iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -j ACCEPT

WHITELIST ON PORTS 
iptables -I INPUT -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j REJECT
iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j ACCEPT

BLACKLIST (assuming default policy is ACCEPT)
iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -j REJECT

BLACKLIST ON PORTS (assuming default policy is ACCEPT)
iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j REJECT