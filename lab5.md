## LAB 5

#### Get Time
```
snmpget -v 2c -c test 127.0.0.1 sysUpTime.0
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (126357) 0:21:03.57
```

#### Get CPU
```
snmpwalk -v 2c -c test 127.0.0.1 | grep CPU
MORE
```


#### CPU Statistics
Load
1 minute Load: .1.3.6.1.4.1.2021.10.1.3.1
5 minute Load: .1.3.6.1.4.1.2021.10.1.3.2
15 minute Load: .1.3.6.1.4.1.2021.10.1.3.3

#### REF
##### CPU
percentage of user CPU time: .1.3.6.1.4.1.2021.11.9.0
raw user cpu time: .1.3.6.1.4.1.2021.11.50.0
percentages of system CPU time: .1.3.6.1.4.1.2021.11.10.0
raw system cpu time: .1.3.6.1.4.1.2021.11.52.0
percentages of idle CPU time: .1.3.6.1.4.1.2021.11.11.0
raw idle cpu time: .1.3.6.1.4.1.2021.11.53.0
raw nice cpu time: .1.3.6.1.4.1.2021.11.51.0
##### Memory Statistics
Total Swap Size: .1.3.6.1.4.1.2021.4.3.0
Available Swap Space: .1.3.6.1.4.1.2021.4.4.0
Total RAM in machine: .1.3.6.1.4.1.2021.4.5.0
Total RAM used: .1.3.6.1.4.1.2021.4.6.0
Total RAM Free: .1.3.6.1.4.1.2021.4.11.0
Total RAM Shared: .1.3.6.1.4.1.2021.4.13.0
Total RAM Buffered: .1.3.6.1.4.1.2021.4.14.0
Total Cached Memory: .1.3.6.1.4.1.2021.4.15.0

#### Get CPU Persent
```
snmpwalk -v 2c -c test 127.0.0.1 .1.3.6.1.4.1.2021.11.9.0
UCD-SNMP-MIB::ssCpuUser.0 = INTEGER: 0

ping -f 127.0.0.1
```

#### Get Information CPU
```
snmpwalk -v 2c -c test 127.0.0.1 .1.3.6.1.4.1.2021.11
MORE
```

#### USE

##### CPU
.1.3.6.1.4.1.2021.11.9.0 = INTEGER: 6  ### CPU User
.1.3.6.1.4.1.2021.11.10.0 = INTEGER: 28  ### CPU System
.1.3.6.1.4.1.2021.11.11.0 = INTEGER: 50  ### CPU Idel
##### RAM
Total RAM in machine: .1.3.6.1.4.1.2021.4.5.0
Total RAM used: .1.3.6.1.4.1.2021.4.6.0
##### DISK
Total size of the disk/partion (kBytes): .1.3.6.1.4.1.2021.9.1.6.1
Available space on the disk: .1.3.6.1.4.1.2021.9.1.7.1
Used space on the disk: .1.3.6.1.4.1.2021.9.1.8.1
Percentage of space used on disk: .1.3.6.1.4.1.2021.9.1.9.1

#### Config Show Diskpart
```
vi /etc/snmp/snmpd.conf
```
End Of file ท้ายไฟล์

```
includeAllDisks 10%

systemctl restart snmpd
```

#### Get DISK Part
```
snmpwalk -v 2c -c test 127.0.0.1 .1.3.6.1.4.1.2021.9.1.2

vi test-snmp2.sh

#!/bin/bash
cpu_use=$(snmpget -v 2c -c test -On 127.0.0.1 .1.3.6.1.4.1.2021.11.9.0|cut -d " " -f 4)
ram_use=$(snmpget -v 2c -c test -On 127.0.0.1 .1.3.6.1.4.1.2021.4.6.0|cut -d " " -f 4)
ram_use_gb=$(echo $ram_use| awk '{$1=$1/(1024^2); print $1,"GB";}')

ram_total=$(snmpget -v 2c -c test -On 127.0.0.1 .1.3.6.1.4.1.2021.4.5.0|cut -d " " -f 4)
ram_use_per=$(( 100 * $ram_use / $ram_total ))

disk_use=$(snmpget -v 2c -c test -On 127.0.0.1 .1.3.6.1.4.1.2021.9.1.8.1|cut -d " " -f 4)
disk_use_gb=$(echo $disk_use| awk '{$1=$1/(1024^2); print $1,"GB";}')
disk_total=$(snmpget -v 2c -c test -On 127.0.0.1 .1.3.6.1.4.1.2021.9.1.6.1|cut -d " " -f 4)
disk_use_per=$(( 100 * $disk_use/$disk_total ))
echo "CPU Use=$cpu_use"
echo "RAM Use=$ram_use_gb"
echo "RAM Use= $ram_use_per %"
echo "DISK Use=$disk_use_gb"
echo "DISK Use path: / = $disk_use_per %"
```

```
yum install php php-snmp -y
```

```
cd /var/www/html/
mkdir 0
cd 0
vi test-snmp.php
```

```
<?php
$sysname = snmp2_get("127.0.0.1", "test", "sysName.0");
echo $sysname;
?>
```

```
php test-snmp.php
```

```
vi cpu-snmp.php
```
```
<?php
$cpu = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.11.9.0");
echo "CPU Use = " . explode(" ", $cpu)[1] . "%";
echo "<br>";

$ram_t = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.4.5.0");
$ram_u = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.4.6.0");
echo "RAM Use = " . number_format((float)(100 * explode(" ", $ram_u)[1]) / explode(" ", $ram_t)[1], 2, '.', '') . "%";
echo "<br>";

$disk_t = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.9.1.6.1");
$disk_u = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.9.1.8.1");
echo "DISK Use = " . number_format((float)(100 * explode(" ", $disk_u)[1]) / explode(" ", $disk_t)[1], 2, '.', '') . "%";
echo "<br>";
?>
```

```
php cpu-snmp.php
```