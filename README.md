# Checkmk-SMART-plugin-4-FreeBSD

SMART plugin to format `smartctl` output suitable for Checkmk agent on FreeBSD

## Motivation

The plugin downloaded from the Checkmk site appears to be oriented towards Linux and is rather complex in order to handle a variety of HBAs. It will likely to be easier to craft a one-off that will work for my specific needs.

## Given

Typical output from the plugin looks like

```text
hbarta@piserver:~ $ sudo /usr/lib/check_mk_agent/plugins/smart 2>/dev/null
<<<smart>>>
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614   1 Raw_Read_Error_Rate     0x000b   100   100   016    Pre-fail  Always       -       0
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614   3 Spin_Up_Time            0x0007   146   146   024    Pre-fail  Always       -       412 (Average 486)
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614   4 Start_Stop_Count        0x0012   100   100   000    Old_age   Always       -       277
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614   5 Reallocated_Sector_Ct   0x0033   100   100   005    Pre-fail  Always       -       0
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614   7 Seek_Error_Rate         0x000b   100   100   067    Pre-fail  Always       -       0
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614   9 Power_On_Hours          0x0012   096   096   000    Old_age   Always       -       29141
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614  10 Spin_Retry_Count        0x0013   100   100   060    Pre-fail  Always       -       0
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614  12 Power_Cycle_Count       0x0032   100   100   000    Old_age   Always       -       95
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614 192 Power-Off_Retract_Count 0x0032   099   099   000    Old_age   Always       -       1453
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614 193 Load_Cycle_Count        0x0012   099   099   000    Old_age   Always       -       1453
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614 194 Temperature_Celsius     0x0002   166   166   000    Old_age   Always       -       36 (Min/Max 15/60)
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614 196 Reallocated_Event_Count 0x0032   100   100   000    Old_age   Always       -       0
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614 197 Current_Pending_Sector  0x0022   100   100   000    Old_age   Always       -       0
HGSTHDN_726060ALE614_DD56419883897 HGSTHDN 726060ALE614 199 UDMA_CRC_Error_Count    0x000a   200   200   000    Old_age   Always       -       1
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160   1 Raw_Read_Error_Rate     0x000b   100   100   050    Pre-fail  Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160   3 Spin_Up_Time            0x0027   100   100   001    Pre-fail  Always       -       1657
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160   4 Start_Stop_Count        0x0032   100   100   000    Old_age   Always       -       203
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160   5 Reallocated_Sector_Ct   0x0033   100   100   050    Pre-fail  Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160   7 Seek_Error_Rate         0x000b   100   100   050    Pre-fail  Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160   9 Power_On_Hours          0x0032   033   033   000    Old_age   Always       -       26813
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160  10 Spin_Retry_Count        0x0033   104   100   030    Pre-fail  Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160  12 Power_Cycle_Count       0x0032   100   100   000    Old_age   Always       -       71
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 191 G-Sense_Error_Rate      0x0032   100   100   000    Old_age   Always       -       10
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 192 Power-Off_Retract_Count 0x0032   100   100   000    Old_age   Always       -       64
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 193 Load_Cycle_Count        0x0032   100   100   000    Old_age   Always       -       204
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 194 Temperature_Celsius     0x0022   100   100   000    Old_age   Always       -       32 (Min/Max 10/53)
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 196 Reallocated_Event_Count 0x0032   100   100   000    Old_age   Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 197 Current_Pending_Sector  0x0032   100   100   000    Old_age   Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 199 UDMA_CRC_Error_Count    0x0032   200   200   000    Old_age   Always       -       1
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 220 Disk_Shift              0x0002   100   100   000    Old_age   Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 222 Loaded_Hours            0x0032   034   034   000    Old_age   Always       -       26681
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 223 Load_Retry_Count        0x0032   100   100   000    Old_age   Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 224 Load_Friction           0x0022   100   100   000    Old_age   Always       -       0
TOSHIBA_HDWE160_DD56419883897 TOSHIBA HDWE160 226 Load-in_Time            0x0026   100   100   000    Old_age   Always       -       542
hbarta@piserver:~ $ 
```

This appears to consist of the following fields:

* some kind of drive ID derived from `Device Model` and information from `/dev/disk/by-id` entries. (`/dev/disk/by-id` do not exist on FreeBSD.( This field could be substituted by drive model (with spaces removed/replaced) and drive serial number, both obtained from `smartctl` output.
* Manufacturer, again found in `smartctl` output.
* Model identifier, also in `smartctl` output.
* Enumerated SMART records.

Drives on the system in question are identified as

```text
[hbarta@mesquite ~]$ ls -l /dev/ada?
crw-r-----  1 root  operator  0x67 Apr 17 13:38 /dev/ada0
crw-r-----  1 root  operator  0x6a Apr 17 13:38 /dev/ada1
crw-r-----  1 root  operator  0x6b Apr 17 13:38 /dev/ada2
crw-r-----  1 root  operator  0x6c Apr 17 13:38 /dev/ada3
crw-r-----  1 root  operator  0x6d Apr 17 13:38 /dev/ada4
[hbarta@mesquite ~]$ 
```

## Deploy & test

```text
sudo cp smart /usr/local/lib/check_mk_agent/plugins
sudo /usr/local/lib/check_mk_agent/plugins/smart
sudo check_mk_agent
```

## Errata

It may be necessary to enable SMART reporting by e.g. `smartctl -s on /dev/adaN`. This is rarely needed on Linux but seems to be frequently needed on FreeBSD.
