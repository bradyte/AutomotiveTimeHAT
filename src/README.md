# Installation

The implementation of `phc2sys` is the same but `ptp4l` has two implementations, one for the grandmaster AutomotiveTimeHAT and one for the client devices connected.
## grandmaster

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
#           so that ptp4l converges faster when “time jumps” occur
sudo wget -O /etc/systemd/system/phc2sys.service \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/phc2sys/phc2sys.service

sudo wget -O /etc/linuxptp/phc2sys.conf \
  https://raw.githubusercontent.com/bradyte/AutomotiveTimeHAT/refs/heads/main/src/phc2sys/phc2sys.conf
```

### enable the daemons

```bash
sudo systemctl daemon-reload
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
#           so that ptp4l converges faster when “time jumps” occur
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
Apr 21 23:02:58 raspberrypi sh[794]: ts2phc[24323.920]: adding tstamp 1776827015.000000001 to clock /dev/ptp1
Apr 21 23:02:58 raspberrypi sh[794]: ts2phc[24323.920]: /dev/ptp1 offset          1 s2 freq  +13299
Apr 21 23:02:59 raspberrypi sh[794]: ts2phc[24324.920]: adding tstamp 1776827015.999999994 to clock /dev/ptp1
Apr 21 23:02:59 raspberrypi sh[794]: ts2phc[24324.920]: /dev/ptp1 offset         -6 s2 freq  +13293
Apr 21 23:03:00 raspberrypi sh[794]: ts2phc[24325.920]: adding tstamp 1776827017.000000001 to clock /dev/ptp1
Apr 21 23:03:00 raspberrypi sh[794]: ts2phc[24325.920]: /dev/ptp1 offset          1 s2 freq  +13298
Apr 21 23:03:01 raspberrypi sh[794]: ts2phc[24326.920]: adding tstamp 1776827018.000000002 to clock /dev/ptp1
Apr 21 23:03:01 raspberrypi sh[794]: ts2phc[24326.920]: /dev/ptp1 offset          2 s2 freq  +13299
Apr 21 23:03:02 raspberrypi sh[794]: ts2phc[24327.920]: adding tstamp 1776827018.999999996 to clock /dev/ptp1
Apr 21 23:03:02 raspberrypi sh[794]: ts2phc[24327.920]: /dev/ptp1 offset         -4 s2 freq  +13294
```

### ptp4l-auto-gm
```bash
Apr 21 23:05:22 raspberrypi ptp4l[10528]: [24468.895] selected /dev/ptp1 as PTP clock
Apr 21 23:05:23 raspberrypi ptp4l[10528]: [24468.944] port 1 (eth1): INITIALIZING to MASTER on INIT_COMPLETE
Apr 21 23:05:23 raspberrypi ptp4l[10528]: [24468.944] port 0 (/var/run/ptp4l): INITIALIZING to LISTENING on INIT_COMPLETE
Apr 21 23:05:23 raspberrypi ptp4l[10528]: [24468.944] port 0 (/var/run/ptp4lro): INITIALIZING to LISTENING on INIT_COMPLETE
Apr 21 23:05:24 raspberrypi ptp4l[10528]: [24470.900] selected local clock f0b2b9.fffe.31a9a8 as best master
Apr 21 23:05:24 raspberrypi bash[10529]: sending: SET GRANDMASTER_SETTINGS_NP
Apr 21 23:05:24 raspberrypi bash[10529]:         f0b2b9.fffe.31a9a8-0 seq 0 RESPONSE MANAGEMENT GRANDMASTER_SETTINGS_NP
Apr 21 23:05:24 raspberrypi bash[10529]:                 clockClass              6
Apr 21 23:05:24 raspberrypi bash[10529]:                 clockAccuracy           0x21
Apr 21 23:05:24 raspberrypi bash[10529]:                 offsetScaledLogVariance 0x4e5d
Apr 21 23:05:24 raspberrypi bash[10529]:                 currentUtcOffset        37
Apr 21 23:05:24 raspberrypi bash[10529]:                 leap61                  0
Apr 21 23:05:24 raspberrypi bash[10529]:                 leap59                  0
Apr 21 23:05:24 raspberrypi bash[10529]:                 currentUtcOffsetValid   1
Apr 21 23:05:24 raspberrypi bash[10529]:                 ptpTimescale            1
Apr 21 23:05:24 raspberrypi bash[10529]:                 timeTraceable           1
Apr 21 23:05:24 raspberrypi bash[10529]:                 frequencyTraceable      1
Apr 21 23:05:24 raspberrypi bash[10529]:                 timeSource              0x20
Apr 21 23:05:25 raspberrypi systemd[1]: Started ptp4l-gm.service - Precision Time Protocol (PTP) service.
```

### phc2sys
```bash
Apr 21 23:07:15 raspberrypi phc2sys[716]: [24581.066] CLOCK_REALTIME phc offset       -23 s2 freq  +22740 delay   2222
Apr 21 23:07:16 raspberrypi phc2sys[716]: [24582.066] CLOCK_REALTIME phc offset        15 s2 freq  +22771 delay   2148
Apr 21 23:07:17 raspberrypi phc2sys[716]: [24583.066] CLOCK_REALTIME phc offset        40 s2 freq  +22800 delay   2204
Apr 21 23:07:18 raspberrypi phc2sys[716]: [24584.067] CLOCK_REALTIME phc offset        25 s2 freq  +22797 delay   2203
Apr 21 23:07:19 raspberrypi phc2sys[716]: [24585.067] CLOCK_REALTIME phc offset       -35 s2 freq  +22745 delay   2222
```

### ptp4l-auto-client
From the client device, I am seeing sub 15ns latency
```bash
Apr 21 16:32:34 raspberrypi ptp4l[918]: ptp4l[1415.461]: master offset         -5 s3 freq  -12640 path delay       -10
Apr 21 16:32:35 raspberrypi ptp4l[918]: ptp4l[1416.462]: master offset          1 s3 freq  -12636 path delay       -10
Apr 21 16:32:36 raspberrypi ptp4l[918]: ptp4l[1417.462]: master offset         -1 s3 freq  -12637 path delay       -10
Apr 21 16:32:37 raspberrypi ptp4l[918]: ptp4l[1418.462]: master offset         -1 s3 freq  -12638 path delay       -10
Apr 21 16:32:38 raspberrypi ptp4l[918]: ptp4l[1419.462]: master offset          2 s3 freq  -12635 path delay       -10
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
Then configure the output, I'm using SDP0 in this case since it's available on both

Additionally, you can use the very convenient check_clocks program to confirm synchronization in the system

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