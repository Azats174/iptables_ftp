# iptables_ftp
Iptables_ftp
modprobe ip_conntrack_ftp
Allow FTP connections on port 21 incoming and outgoing

iptables -A INPUT  -p tcp -m tcp --dport 21 -m conntrack --ctstate ESTABLISHED,NEW -j ACCEPT -m comment --comment "Allow ftp connections on port 21"
iptables -A OUTPUT -p tcp -m tcp --dport 21 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT -m comment --comment "Allow ftp connections on port 21"

Allow FTP port 20 for active connections incoming and outgoing

iptables -A INPUT  -p tcp -m tcp --dport 20 -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT -m comment --comment "Allow ftp connections on port 20"
iptables -A OUTPUT -p tcp -m tcp --dport 20 -m conntrack --ctstate ESTABLISHED -j ACCEPT -m comment --comment "Allow ftp connections on port 20"

Finally allow FTP passive inbound traffic

iptables -A INPUT  -p tcp -m tcp --sport 1024: --dport 1024: -m conntrack --ctstate ESTABLISHED -j ACCEPT -m comment --comment "Allow passive inbound connections"
iptables -A OUTPUT -p tcp -m tcp --sport 1024: --dport 1024: -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT -m comment --comment "Allow passive inbound connections"

FWIW, it seems that there was a change in kernel 4.7, so that you either need to set 
echo "net.netfilter.nf_conntrack_helper=1" > /etc/sysctl.conf
and add rule
iptables -A PREROUTING -t raw -p tcp --dport 21 -j CT --helper ftp  --comment  "Enable ftp helper"

