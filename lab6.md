## LAB6

```
net-snmp-create-v3-user --help
```

Apparently at least one snmpd demon is already running.
You must stop them in order to use this command.

```
systemctl stop snmpd
net-snmp-create-v3-user --help
```

Usage:
  net-snmp-create-v3-user [-ro] [-A authpass] [-X privpass]
                          [-a MD5|SHA] [-x DES|AES] [username]

```
net-snmp-create-v3-user -A test -X test1 -a SHA -x AES testv3
```

adding the following line to /var/lib/net-snmp/snmpd.conf:
```
net-snmp-create-v3-user -A test -X test1 -a SHA -x AES testv3
```
adding the following line to /etc/snmp/snmpd.conf:
```
rwuser testv3
```

more /var/lib/net-snmp/snmpd.conf
```
createUser testv3 SHA "test" AES test1
```

```
more /etc/snmp/snmpd.conf
rwuser testv3

systemctl restart snmpd
```

```
more /var/lib/net-snmp/snmpd.conf
------------------------------------------------------------
usmUser
------------------------------------------------------------

systemctl stop snmpd

more /var/lib/net-snmp/snmpd.conf
------------------------------------------------------------
usmUser
------------------------------------------------------------
```
```
vi /etc/snmp/snmpd.conf
Delect -> rwuser ……..

net-snmp-create-v3-user -A test12345678 -X test12345678 -a SHA -x AES testv3
```

adding the following line to /var/lib/net-snmp/snmpd.conf:
```
createUser testv3 SHA "test12345678" AES test12345678
```
adding the following line to /etc/snmp/snmpd.conf:
```
rwuser testv3
```
   
```
service snmpd start

snmpget -v 3 -u testv3 -l authNoPriv -a SHA -A test12345678 127.0.0.1 sysUpTime.0
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (20561) 0:03:25.61

snmpget -v 3 -u testv3 -l authPriv  -a SHA -A test12345678 -x AES -X test12345678 127.0.0.1 sysUpTime.0
DISMAN-EVENT-MIB::sysUpTimeInstance = Timeticks: (22121) 0:03:41.21

snmpwalk -v 3 -u testv3 -l authPriv  -a SHA -A test12345678 -x AES -X test12345678 127.0.0.1 | more
 ```
 
 
https://developers.google.com/chart/?hl=th


```
mkdir /var/www/html/1
cd /var/www/html/1
vi test-cpu.php
```

-------------------------------------------
```
<html>
  <head>
   <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
   <script type="text/javascript">
      google.charts.load('current', {'packages':['gauge']});
      google.charts.setOnLoadCallback(drawChart);

      function drawChart() {

        var data = google.visualization.arrayToDataTable([
          ['Label', 'Value'],
          ['Memory', 10],
          ['CPU', 45],
          ['Network', 70]
        ]);

        var options = {
          width: 400, height: 120,
          redFrom: 90, redTo: 100,
          yellowFrom:75, yellowTo: 90,
          minorTicks: 5
        };

        var chart = new google.visualization.Gauge(document.getElementById('chart_div'));

        chart.draw(data, options);

        setInterval(function() {
          data.setValue(0, 1, 40 + Math.round(60 * Math.random()));
          chart.draw(data, options);
        }, 13000);
        setInterval(function() {
          data.setValue(1, 1, 40 + Math.round(60 * Math.random()));
          chart.draw(data, options);
        }, 5000);
        setInterval(function() {
          data.setValue(2, 1, 60 + Math.round(20 * Math.random()));
          chart.draw(data, options);
        }, 26000);
      }
    </script>
  </head>
  <body>
    <div id="chart_div" style="width: 400px; height: 120px;"></div>
  </body>
</html>
```
-------------------------------------------
Moddd

-------------------------------------------
```
<?php
$cpu = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.11.9.0");
//echo "CPU Use = " . explode(" ", $cpu)[1] . "%";
//echo "<br>";

$ram_t = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.4.5.0");
$ram_u = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.4.6.0");
//echo "RAM Use = " . number_format((float)(100 * explode(" ", $ram_u)[1]) / explode(" ", $ram_t)[1], 2, '.', '') . "%";
//echo "<br>";

$disk_t = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.9.1.6.1");
$disk_u = snmp2_get("127.0.0.1", "test", ".1.3.6.1.4.1.2021.9.1.8.1");
//echo "DISK Use = " . number_format((float)(100 * explode(" ", $disk_u)[1]) / explode(" ", $disk_t)[1], 2, '.', '') . "%";
//echo "<br>";
?>
<html>
  <head>
   <meta http-equiv="refresh" content="5" />
   <script type="text/javascript" src="https://www.gstatic.com/charts/loader.js"></script>
   <script type="text/javascript">
      google.charts.load('current', {'packages':['gauge']});
      google.charts.setOnLoadCallback(drawChart);

      function drawChart() {

        var data = google.visualization.arrayToDataTable([
          ['Label', 'Value'],
          ['Memory', <?php echo number_format((float)(100 * explode(" ", $ram_u)[1]) / explode(" ", $ram_t)[1], 2, '.', '')  ?>],
          ['CPU', <?php echo explode(" ", $cpu)[1] ?>],
        ]);

        var options = {
          width: 400, height: 120,
          redFrom: 90, redTo: 100,
          yellowFrom:75, yellowTo: 90,
          minorTicks: 5
        };

        var chart = new google.visualization.Gauge(document.getElementById('chart_div'));

        chart.draw(data, options);
		setInterval(function() {
          data.setValue(0, 1, 40 + Math.round(60 * Math.random()));
          chart.draw(data, options);
        }, 13000);
        setInterval(function() {
          data.setValue(1, 1, 40 + Math.round(60 * Math.random()));
          chart.draw(data, options);
        }, 5000);
        setInterval(function() {
          data.setValue(2, 1, 60 + Math.round(20 * Math.random()));
          chart.draw(data, options);
        }, 26000);
      }
    </script>
  </head>
  <body>
    <div id="chart_div" style="width: 400px; height: 120px;"></div>
  </body>
</html>
``````
-------------------------------------------