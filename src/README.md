# Installation

The implementation of `phc2sys` is the same but `ptp4l` has two implementations, one for the grandmaster AutomotiveTimeHAT and one for the client devices connected.
## grandmaster

### configure igc-ptp-irq
```bash
#    This systemd service allows "other" eth1 events to be prioritized over
#     other TxR IRQs. Needed for handling the multiple SDP EXTTS events
sudo wget -O /etc/systemd/system/ptp4l-auto-gm.service \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/igc/igc-ptp-irq.service
```

### configure ptp4l-auto-gm
```bash
#    This systemd service is the workhorse pulling it all together:
#      * -i specifies ptp port
#      * the .conf file declares the high quality of the clock 
#      *    at the bottom of the linuxptp automotive-master.cfg
#      * pmc then uses a non-portable command to manually set the 
#      *    currentUtcOffsetValid to 1 for phc2sys to trust this clock
sudo wget -O /etc/systemd/system/ptp4l-auto-gm.service \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/ptp4l/ptp4l-auto-gm.service

sudo wget -O /etc/linuxptp/ptp4l-auto-gm.conf \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/ptp4l/ptp4l-auto-gm.conf
```

### configure phc2sys
```bash
#    This systemd service:
#      * -s specifies source clock /dev/ptp1
#      * -c specifies time sink CLOCK_REALTIME
#      * -w wait until ptp4l is synchronized
#      * set transportSpecific to 1 and set_threshold  
#           so that ptp4l converges faster when "time jumps" occur
sudo wget -O /etc/systemd/system/phc2sys.service \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/phc2sys/phc2sys.service

sudo wget -O /etc/linuxptp/phc2sys.conf \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/phc2sys/phc2sys.conf
```

### enable the daemons

```bash
sudo systemctl daemon-reload
sudo systemctl enable igc-ptp-irq.service
sudo systemctl start igc-ptp-irq.service
sudo systemctl enable ptp4l-auto-gm.service
sudo systemctl start ptp4l-auto-gm.service
sudo systemctl enable phc2sys.service
sudo systemctl start phc2sys.service
```

## client

### configure ptp4l-auto-client
```bash
#    This systemd service set the client for a static role:
#      * -i specifies ptp port
#      * the .conf file declares the static configuration of the client to look
#      *    at the port and sync immediately
sudo wget -O /etc/systemd/system/ptp4l-auto-client.service \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/ptp4l/ptp4l-auto-client.service

sudo wget -O /etc/linuxptp/ptp4l-auto-client.conf \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/ptp4l/ptp4l-auto-client.conf
```

### configure phc2sys
```bash
#    This systemd service:
#      * -s specifies source clock /dev/ptp1
#      * -c specifies time sink CLOCK_REALTIME
#      * -w wait until ptp4l is synchronized
#      * set transportSpecific to 1 and set_threshold  
#           so that ptp4l converges faster when time jumps occur
sudo wget -O /etc/systemd/system/phc2sys.service \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/phc2sys/phc2sys.service

sudo wget -O /etc/linuxptp/phc2sys.conf \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/phc2sys/phc2sys.conf
```

### enable the daemons

```bash
sudo systemctl daemon-reload
sudo systemctl enable ptp4l-auto-client.service
sudo systemctl start ptp4l-auto-client.service
sudo systemctl enable phc2sys.service
sudo systemctl start phc2sys.service
```
## Example Output

### ts2phc
```bash
un 08 02:13:43 ubuntu sh[824]: ts2phc[3759.348]: adding tstamp 1780884860.000000001 to clock /dev/ptp1
Jun 08 02:13:43 ubuntu sh[824]: ts2phc[3759.348]: /dev/ptp1 offset          1 s2 freq  +12347
Jun 08 02:13:44 ubuntu sh[824]: ts2phc[3760.348]: adding tstamp 1780884861.000000000 to clock /dev/ptp1
Jun 08 02:13:44 ubuntu sh[824]: ts2phc[3760.348]: /dev/ptp1 offset          0 s2 freq  +12346
Jun 08 02:13:45 ubuntu sh[824]: ts2phc[3761.348]: adding tstamp 1780884862.000000006 to clock /dev/ptp1
Jun 08 02:13:45 ubuntu sh[824]: ts2phc[3761.348]: /dev/ptp1 offset          6 s2 freq  +12352
Jun 08 02:13:46 ubuntu sh[824]: ts2phc[3762.348]: adding tstamp 1780884863.000000000 to clock /dev/ptp1
Jun 08 02:13:46 ubuntu sh[824]: ts2phc[3762.348]: /dev/ptp1 offset          0 s2 freq  +12348
Jun 08 02:13:47 ubuntu sh[824]: ts2phc[3763.348]: adding tstamp 1780884863.999999998 to clock /dev/ptp1
Jun 08 02:13:47 ubuntu sh[824]: ts2phc[3763.348]: /dev/ptp1 offset         -2 s2 freq  +12346
Jun 08 02:13:48 ubuntu sh[824]: ts2phc[3764.348]: adding tstamp 1780884865.000000004 to clock /dev/ptp1
Jun 08 02:13:48 ubuntu sh[824]: ts2phc[3764.348]: /dev/ptp1 offset          4 s2 freq  +12351
```

### ptp4l-auto-gm
```bash
Jun 08 01:46:27 ubuntu systemd[1]: Starting ptp4l-auto-gm.service - Precision Time Protocol (PTP) service...
Jun 08 01:46:27 ubuntu ptp4l[1739]: [2123.452] selected /dev/ptp1 as PTP clock
Jun 08 01:46:27 ubuntu ptp4l[1739]: [2123.474] port 1 (eth1): INITIALIZING to MASTER on INIT_COMPLETE
Jun 08 01:46:27 ubuntu ptp4l[1739]: [2123.475] port 0 (/var/run/ptp4l): INITIALIZING to LISTENING on INIT_COMPLETE
Jun 08 01:46:27 ubuntu ptp4l[1739]: [2123.475] port 0 (/var/run/ptp4lro): INITIALIZING to LISTENING on INIT_COMPLETE
Jun 08 01:46:29 ubuntu ptp4l[1739]: [2125.560] selected local clock f0b2b9.fffe.31a9a8 as best master
Jun 08 01:46:29 ubuntu pmc[1755]: sending: SET GRANDMASTER_SETTINGS_NP
Jun 08 01:46:29 ubuntu pmc[1755]:         f0b2b9.fffe.31a9a8-0 seq 0 RESPONSE MANAGEMENT GRANDMASTER_SETTINGS_NP
Jun 08 01:46:29 ubuntu pmc[1755]:                 clockClass              6
Jun 08 01:46:29 ubuntu pmc[1755]:                 clockAccuracy           0x21
Jun 08 01:46:29 ubuntu pmc[1755]:                 offsetScaledLogVariance 0x4e5d
Jun 08 01:46:29 ubuntu pmc[1755]:                 currentUtcOffset        37
Jun 08 01:46:29 ubuntu pmc[1755]:                 leap61                  0
Jun 08 01:46:29 ubuntu pmc[1755]:                 leap59                  0
Jun 08 01:46:29 ubuntu pmc[1755]:                 currentUtcOffsetValid   1
Jun 08 01:46:29 ubuntu pmc[1755]:                 ptpTimescale            1
Jun 08 01:46:29 ubuntu pmc[1755]:                 timeTraceable           1
Jun 08 01:46:29 ubuntu pmc[1755]:                 frequencyTraceable      1
Jun 08 01:46:29 ubuntu pmc[1755]:                 timeSource              0x20
Jun 08 01:46:29 ubuntu systemd[1]: Started ptp4l-auto-gm.service - Precision Time Protocol (PTP) service.
```

### phc2sys
```bash
Jun 08 02:15:08 ubuntu phc2sys[5127]: [3845.277] CLOCK_REALTIME phc offset        -1 s2 freq  +22104 delay   2093
Jun 08 02:15:09 ubuntu phc2sys[5127]: [3846.277] CLOCK_REALTIME phc offset       -13 s2 freq  +22091 delay   2093
Jun 08 02:15:10 ubuntu phc2sys[5127]: [3847.278] CLOCK_REALTIME phc offset        -5 s2 freq  +22095 delay   2074
Jun 08 02:15:11 ubuntu phc2sys[5127]: [3848.278] CLOCK_REALTIME phc offset        -1 s2 freq  +22098 delay   2074
Jun 08 02:15:12 ubuntu phc2sys[5127]: [3849.278] CLOCK_REALTIME phc offset        10 s2 freq  +22109 delay   2092
Jun 08 02:15:13 ubuntu phc2sys[5127]: [3850.279] CLOCK_REALTIME phc offset         9 s2 freq  +22111 delay   2092
```

### ptp4l-auto-client
From the client device, I am seeing sub 15ns latency
```bash
Jun 07 22:28:18 raspberrypi ptp4l[986]: [310081.094] rms    5 max    8 freq +15757 +/-   4 delay    43 +/-   0
Jun 07 22:28:19 raspberrypi ptp4l[986]: [310082.095] rms    4 max    8 freq +15754 +/-   6 delay    44 +/-   0
Jun 07 22:28:20 raspberrypi ptp4l[986]: [310083.097] rms    5 max   11 freq +15751 +/-   6 delay    43 +/-   0
Jun 07 22:28:21 raspberrypi ptp4l[986]: [310084.099] rms    5 max    9 freq +15750 +/-   6 delay    44 +/-   0
Jun 07 22:28:22 raspberrypi ptp4l[986]: [310085.100] rms    6 max    8 freq +15750 +/-   8 delay    44 +/-   0
```

## confirming sync
Assuming you have a similar setup with the ability to use the SDP pins on the Intel NICs, you can also confirm the latency by scoping the PPS output signals.

```bash
# download testptp
wget https://raw.githubusercontent.com/torvalds/linux/master/tools/testing/selftests/ptp/testptp.c

# compile the code
gcc -Wall -lrt testptp.c -o testptp

# move to binary directory
sudo mv testptp /usr/local/bin/
```
Then configure the output, I'm using SDP0 in this case since it's available on both.
```bash
sudo testptp -d /dev/ptp1 -L 0,2
sudo testptp -d /dev/ptp1 -p 1000000000
```

Additionally, you can use the very convenient check_clocks program to confirm synchronization in the system. However, I did notice some issues when it interacts with the Automotive profile for now.
```bash
# download check_clocks
wget https://tsn.readthedocs.io/_downloads/5d05630fceb3e7ac1582bc94c7468fc3/check_clocks.c

# compile the code
gcc -Wall -lrt check_clocks.c -o check_clocks

# move to binary directory
sudo mv check_clocks /usr/local/bin/
```

## Debugging
Just some things I copy and pasted many times.
```bash
journalctl -u ts2phc-gps.service -f
journalctl -u ptp4l-auto-gm.service -f
journalctl -u ptp4l-auto-client.service -f
journalctl -u phc2sys.service -f
```

Immediate Fix: Disable System Time Daemons
```bash
# Disable the default systemd time sync
sudo systemctl stop systemd-timesyncd
sudo systemctl disable systemd-timesyncd

# If you have chrony or ntp installed, stop them too
sudo systemctl stop chrony
sudo systemctl stop ntp

# Disable NTP if it is running
sudo timedatectl set-ntp false
```

Check pmc settings
```bash
sudo pmc -u -b 0 -t 1 "GET GRANDMASTER_SETTINGS_NP"

sudo phc_ctl eth1 cmp
```
