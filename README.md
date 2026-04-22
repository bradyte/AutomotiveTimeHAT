# AutomotiveTimeHAT

<img width="1020" height="768" alt="PXL_20260421_134043481" src="https://github.com/user-attachments/assets/0bfda747-5b7d-4ddb-8528-48f0428d3eb2" />

Inspired by the [Time Appliances TimeHAT](https://github.com/Time-Appliances-Project/TimeHAT) I wanted to build my own setup leveraging an existing CM4 I had. Also, I wanted to understand the individual components, the ZED-F9T and the i226 NIC, better so I opted for development boards cobbled together. The TimeHAT is an optimized and clean approach, mine is more academic.

Additionally, once ts2phc is successfully synchronized, this implementation takes it a step further to add ptp4l and phc2sys implementation for an 802.1AS synchronized network ultimately for transporting audio, video, and control on a time-sensitive network.

## Changes from the original TimeHAT repo
### hardware
This setup uses a CM4 which is different than the Raspberry Pi 5 but the igc driver fix does still appear to work in conjunction with my external i226 NIC for the `rising` edge instead of `both` edge trigger, so thanks [Time Appliances](https://github.com/Time-Appliances-Project)!

I sped up the UART rate of the ZED-F9T to 115200 to ensure the NMEA sentences arrive as soon as possible avoiding issues aligning with the PPS edge.

The CM4 board has an RTC so also leveraging using that for faster time convergence after power down.

### software
I also added scheduling priority on all the services to ensure these are top priority in the OS for real-time applications.

```
CPUSchedulingPolicy=rr
CPUSchedulingPriority=98
```

Aside from that, the original [Time Appliances TimeHAT](https://github.com/Time-Appliances-Project/TimeHAT) repo does an excellent job explaining how to get setup so I advise starting there in your system.

## Important differences from a standard PTP setup

The most troubleshooting was around the fact that 802.1AS requires a transportSpecific flag to be set. This configuration is also intended for more of a static, automotive implementation so the ptp4l configurations will designate the clock parameters for a grandmaster time transmitter and time receiver explicitly. I leveraged the `automotive-master.cfg` as the automotive profile for server and `automotive-slave.cfg` as the automotive profile for client.



## License

This project is licensed under the Creative Commons Attribution-NonCommercial 4.0 International License (CC BY-NC 4.0).

You are free to:

Share — copy and redistribute the material in any medium or format
Adapt — remix, transform, and build upon the material
Under the following terms:

Attribution — You must give appropriate credit, provide a link to the license, and indicate if changes were made.
NonCommercial — You may not use the material for commercial purposes.
For full details, see: https://creativecommons.org/licenses/by-nc/4.0/

As the project creator, I reserve the right to use this material commercially or under any other terms.
