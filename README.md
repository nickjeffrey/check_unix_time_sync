# check_unix_time_sync
nagios check for NTP time sync on UNIX-like operating systems (AIX, Linux, *BSD, etc)

Supports the following time daemons: ntpd, xntpd, chronyd, systemctl-timesyncd 

This script is executed remotely on a monitored system by the NRPE or check_by_ssh methods available in nagios.

If you are using the check_by_ssh method, you will need a section in the services.cfg
file on the nagios server that looks similar to the following.
This assumes that you already have ssh key pairs configured.
```
   # Define service for checking time synchronization 
   define service{
           use                             generic-service
           host_name                       unix11
           service_description             time sync
           check_command                   check_by_ssh!"/usr/local/nagios/libexec/check_unix_time_sync"
           }
```

If you are using the check_nrpe method, you will need a section in the services.cfg
file on the nagios server that looks similar to the following.
This assumes that you already have ssh key pairs configured.
```
   # Define service for checking time synchronization 
   define service{
           use                             generic-service
           host_name                       unix11
           service_description             time sync
           check_command                   check_nrpe!check_unix_time_sync -t 30
           }
```

If using NRPE, you will also need a section defining the NRPE command in the /usr/local/nagios/nrpe.cfg file that looks like this:
```
command[check_unix_time_sync]=/usr/local/nagios/libexec/check_unix_time_sync
```

Output should look similar to one of the following (depending on time daemon being used)
```
time sync OK  OS=Linux daemon=ntpd  timeserver:10.10.20.7 stratum:3 lastupdate:348s offset:0.820ms |  offset:1ms;;;;

time sync OK  OS=Linux daemon=chronyd  timeserver:0.pool.ntp.org stratum:2 lastupdate:523s offset:4ms  timeserver:1.pool.ntp.org stratum:2 lastupdate:337s offset:1ms  timeserver:time.nist.gov stratum:1 lastupdate:51s offset:3ms  |  offset:2ms;;;;

time sync OK  OS=AIX daemon=ntpd  timeserver:ntp1.example.com stratum:3 lastupdate:562s offset:10.421ms  timeserver:ntp2.example.com stratum:2 lastupdate:386s offset:10.327ms |  offset:10ms;;;;

time sync OK  OS=Linux daemon=systemd-timesyncd timeserver:ntp1.example.com stratum:1 lastupdate:1648s offset:20ms |  offset=20ms;;;;

time sync WARN - time on this machine off by 101 milliseconds from time server ntp1.example.com.  OS=Linux daemon=ntpd  timeserver:ntp1.example.com stratum:3 lastupdate:812s offset:101.260ms |  offset:101ms;;;;

time sync WARN - no sync with time server ntp1.example.com for more than 24 hours.  OS=Linux daemon=ntpd  timeserver:ntp1.example.com stratum:3 lastupdate:86499s offset:12.640ms |  offset:12ms;;;;

time sync WARN - the stratum of time server ntp1.example.com is 12.  This is still a valid stratum, but we prefer to sync against time servers with a stratum between 1 and 5.  Please review output of ntptrace, ntpq -p, or chronyc sources commands.  It may be that a time server we sync against has lowered its stratum because it is becoming less reliable, or it has been unable to sync with an upstream provider for some time.  See if you can get the stratum back down to a lower value. OS=Linux daemon=ntpd  timeserver:ntp1.example.com stratum:12 lastupdate:86499s offset:12.640ms |  offset:12ms;;;;

```

