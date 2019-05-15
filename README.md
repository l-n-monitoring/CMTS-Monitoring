# CMTS - Cable Monitoring
> Monitoring of Cable characteristics on CMTS was always a hard job. You had to either implement your CMTS to Cacti/Zabbix or create your own polling application. I would like to share with you how I made it easy with opensource tools freely available on the internet.

**Prerequirements**
- <a href="https://docs.influxdata.com/influxdb/v1.7/introduction/installation/">InfluxDB</a>
- <a href="https://docs.influxdata.com/telegraf/v1.10/introduction/installation/">Telegraf</a>
- <a href="https://grafana.com/docs/installation/">Grafana</a>
- net-snmp

**CMTS Configuration**
> InfluxDB is all about tags. Tags are used to query and also to display values in a propper way. In order for data to be displayed we need to set descriptions and hostname of the CMTS propperly.

***Hostname***
> Name your CMTS something meaningful. My is called CMTS-Testlab-01.
```
hostname CMTS-Testlab-01
```
***Fibernode***
> Give your fibernode also a meaningful description. My fibernode is called by street on which it's located.
```
cable fiber-node "FN1" 
 description "Street 1"
```
***Upstreams***
> All upstream interfaces and subinterfaces connected to this fibernode must have exactly the same description as fibernode. Do this for all upstreams connected to this fibernode.
```
interface cable-upstream 11/3 
 description "Street 1"
interface cable-upstream 11/3.0
 description "Street 1"
interface cable-upstream 11/3.1
 description "Street 1"
```
***Downstreams***
> As downstream interface can be connected to various fibernodes you need to have somekind of delimiter. I have chosen pipe "|" as the one. So let's say we have 2 fibernodes connected to one downstream. Than the description should look like: "|Street 1|Street 2|". Do this for all downstreams connected to this mac-domain.
```
interface cable-downstream 14/8 
 description "|Street 1|Street 2|"
```
**Telegraf**
> Location of telegraf config files is "/etc/telegraf/telegraf.d/".
- CMTS_generic.conf should work with every single cmts. However it doesn't show CM counters as this is vendor specific.
- CMTS_Cisco.conf should work with Cisco CMTSes. It will show CM counters. Sadly it doesn't show Docsis 3.0 modems.
- CMTS_Casa.conf should work with Casa-Systems CMTSes. Also enviroment measurement is done.

> Edit desired config, change IP and community. Copy config to telegraf config directory and reload telegraf service.
```
#cp CMTS_generic.conf /etc/telegraf/telegraf.d/CMTS_Testlab_01.conf 
#service telegraf reload
```
**Grafana**
> Login to your grafana server: http://yourserver.ip:3000/ (admin/admin by default)

***Add Data Source***
> Let's create data source called InfluxDB
[![Add Data Source](https://i.imgur.com/C6DVIlB.jpg)]()
***Save Data Source***
> Probably influxdb is running on the same server. Database is called "telegraf" and also datasource should be called "telegraf".
[![Add Data Source](https://i.imgur.com/jqY7Ohn.jpg)]()


