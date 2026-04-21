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