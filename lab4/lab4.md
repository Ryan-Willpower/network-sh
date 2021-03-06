## LAB 4

```
vi /etc/snmp/snmpd.conf
```

ADD Under view systemview included .1

```
rwcommunity test1
systemctl restart snmpd
```


USAGE: snmpwalk [OPTIONS] AGENT [OID]

-v 1|2c|3             specifies SNMP version to use
-c COMMUNITY          set the community string

```
snmpwalk -v 1 -c test -On 127.0.0.1

snmpwalk -v 1 -c test -Of 127.0.0.1

snmpget -v 1 -c test 127.0.0.1 .1.3.6.1.2.1.1.5.0
SNMPv2-MIB::sysName.0 = STRING: server.test1.com

snmpget -v 1 -c test -On 127.0.0.1 .1.3.6.1.2.1.1.5.0
.1.3.6.1.2.1.1.5.0 = STRING: server.test1.com

snmpget -v 1 -c test -On 127.0.0.1 .1.3.6.1.2.1.1.5 Error in packet

snmpgetnext -v 1 -c test -On 127.0.0.1 .1.3.6.1.2.1.1.5.0
.1.3.6.1.2.1.1.6.0 = STRING: Unknown (edit /etc/snmp/snmpd.conf)
```

USAGE: snmpset [OPTIONS] AGENT OID TYPE VALUE [OID TYPE VALUE]

```
snmpset -v 1 -c test 127.0.0.1 1.3.6.1.2.1.1.5.0 i 10
1.3.6.1.2.1.1.5.0: Bad variable type (Type of attribute is OCTET STRING, not INTEGER)

snmpset -v 1 -c test 127.0.0.1 1.3.6.1.2.1.1.5.0 s "abc.test1.com"
Error in packet.

snmpset -v 1 -c test1 127.0.0.1 1.3.6.1.2.1.1.5.0 s "sv.netslab.io"
SNMPv2-MIB::sysName.0 = STRING: sv.netslab.io
```

Show Hostname
```
snmpget -v 1 -c test1 -On 127.0.0.1 .1.3.6.1.2.1.1.5.0 | cut -d " " -f 4
```

Find Ip Next To 127.0.0.1
```
snmpwalk -v 1 -c test -On 127.0.0.1 | grep ".1.3.6.1.2.1.4"
```

Create Shell Scripts
```
vi test-snmp.sh
```

Edit test-snmp.sh
```
#!/bin/bash
host=$(snmpget -v 1 -c test -On 127.0.0.1 .1.3.6.1.2.1.1.5.0|cut -d " " -f 4)
ip=$(snmpgetnext -v 1 -c test -On 127.0.0.1 .1.3.6.1.2.1.4.20.1.1.127.0.0.1 |cut -d " " -f 4)
ram=$(snmpget -v 1 -c test -On 127.0.0.1 .1.3.6.1.4.1.2021.4.5.0 |cut -d " " -f 4)
ram_gb=$(echo $ram| awk '{$1=$1/(1024^2); print $1,"GB";}')
echo "hostname=$host"
echo "ip=$ip"
echo "ram=$ram_gb"
```

```
chmod a+x test-snmp.sh
./test-snmp.sh
```


Get mem
```
snmpwalk -v 1 -c test 127.0.0.1 UCD-SNMP-MIB:memory
snmpwalk -v 1 -c test -On 127.0.0.1 UCD-SNMP-MIB:memory
```