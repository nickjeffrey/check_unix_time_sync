# check_unix_time_sync
nagios check for NTP time sync on UNIX-like operating systems (AIX, Linux, *BSD, etc)

This script is executed remotely on a monitored system by the NRPE or check_by_ssh methods available in nagios.

If you are using the check_by_ssh method, you will need a section in the services.cfg
file on the nagios server that looks similar to the following.
This assumes that you already have ssh key pairs configured.
```
   # Define service for checking time synchronization 
   define service{
           use                             generic-24x7-service
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
           use                             generic-24x7-service
           host_name                       unix11
           service_description             time sync
           check_command                   check_nrpe!check_unix_time_sync -t 30
           }
```

If using NRPE, you will also need a section defining the NRPE command in the /usr/local/nagios/nrpe.cfg file that looks like this:
```
command[check_unix_time_sync]=/usr/local/nagios/libexec/check_unix_time_sync
```


