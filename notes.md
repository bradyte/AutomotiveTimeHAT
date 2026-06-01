##### hwclock
not included by default
```
sudo apt-get install util-linux-extra
```

add to config.txt
```
dtparam=i2c_vc=on
dtoverlay=i2c-rtc,pcf85063a,i2c_csi_dsi
```

##### ublox gps
disable the BT connection on the board

add to config.txt 
```
dtoverlay=disable-bt
```

```bash
sudo pmc -u -b 0 -t 1 \
    "SET GRANDMASTER_SETTINGS_NP \
    clockClass 6 \
    clockAccuracy 0x21 \
    offsetScaledLogVariance 0x4e5d \
    currentUtcOffset 37 \
    leap61 0 \
    leap59 0 \
    currentUtcOffsetValid 1 \
    ptpTimescale 1 \
    timeTraceable 1 \
    frequencyTraceable 1 \
    timeSource 0x20"
```