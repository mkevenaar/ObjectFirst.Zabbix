# Object First OOTBI Cluster by HTTP

[![Version](https://img.shields.io/github/v/release/mkevenaar/ObjectFirst.Zabbix.svg?color=brightgreen&label=version)](https://github.com/mkevenaar/ObjectFirst.Zabbix/releases/latest)
[![Released](https://img.shields.io/badge/dynamic/json.svg?color=brightgreen&label=released&url=https://api.github.com/repos/mkevenaar/ObjectFirst.Zabbix/releases&query=$[0].published_at)](https://github.com/mkevenaar/ObjectFirst.Zabbix/releases/latest)
![GitHub Releases (all releases)](https://img.shields.io/github/downloads/mkevenaar/ObjectFirst.Zabbix/total.svg)

<!--ts-->

<!--te-->

## Overview

This template is designed to monitor an Object First OOTBI Cluster.

## Requirements

Zabbix version: 7.0 and higher.

## Tested versions

This template has been tested on Object First OOTBI 1.5.54.10104 (VSA).

## Setup

1. Download the latest [release](https://github.com/mkevenaar/ObjectFirst.Zabbix/releases/latest)
1. Create a user to monitor the service or use an existing user.
1. Create a host with the name of your cluster and link the `Object First OOTBI Cluster by HTTP` template to it.
1. Configure the following macros: {$OOTBI.API.URL}, {$OOTBI.CLUSTER.ID}, {$OOTBI.USER}, and {$OOTBI.PASSWORD} ([details](#macros-for-object-first-ootbi-cluster-by-http))

## Template: Object First OOTBI Cluster by HTTP

This template is designed to monitor an Object First OOTBI Cluster.

### Screenshots for Object First OOTBI Cluster by HTTP

![Items](./media/cluster_items.png)

![Triggers](./media/cluster_triggers.png)

![Discovery](./media/cluster_discovery.png)

### Macros for Object First OOTBI Cluster by HTTP

|Name|Description|Default|
|----|-----------|-------|
|{$OOTBI.API.URL}|The OOTBI Cluster endpoint is a URL or IP in the format `<scheme>://<host>:<port>`. This needs to be the pointing to the Service Point of your OOTBI Cluster|`https://localhost:8443`|
|{$OOTBI.CLUSTER.ID}|The OOTBI Cluster ID (UUID). The Cluster UUID can be found in Settings. Optional, defaults to first cluster.||
|{$OOTBI.HTTP.PROXY}|Sets the HTTP proxy to `http_proxy` value. If this parameter is empty, then no proxy is used.||
|{$OOTBI.PASSWORD}|The `password` of the Object First OOTBI Cluster account. It is used to obtain an access token.||
|{$OOTBI.USER}|The `username` of the Object First OOTBI Cluster account. It is used to obtain an access token.||
|{$OOTBI.DATA.TIMEOUT}|A response timeout for the API.|`10`|

### Items for Object First OOTBI Cluster by HTTP

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get metrics|The result of API requests is expressed in the JSON.|Script|ootbi.get.metrics|
|Get errors|The errors from API requests.|Dependent item|ootbi.get.errors<br />**Preprocessing**<ul><li>JSON Path: `$.error`⛔️Custom on fail: Set value to</li><li>Discard unchanged with heartbeat: `1h`</li></ul>|
|Cluster Status|Get the cluster status|Dependent item|ootbi.get.cluster.status<br />**Preprocessing**<ul><li>JSON Path: `$.cluster.status`</li><li>Discard unchanged with heartbeat: `1h`</li></ul>|

### Triggers for Object First OOTBI Cluster by HTTP

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Object First OOTBI: Cluster is not healthy|<p>Cluster reports it's not healthy. Please inspect the cluster.</p>|`find(/Object First OOTBI Cluster by HTTP/ootbi.get.cluster.status,,"like","OK")=0`|High||
|Object First OOTBI: There are errors in requests to API|<p>Zabbix has received errors in response to API requests.</p>|`length(last(/Object First OOTBI Cluster by HTTP/ootbi.get.errors))>0`|Average||

### LLD rule Node discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Node discovery|<p>Discovery of cluster nodes.</p>|Dependent item|ootbi.node.discovery<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.cluster.nodes[:]`</p></li><li><p>Discard unchanged with heartbeat: `6h`</p></li></ul>|

### Host Prototypes for Node discovery

|Name|Templates|Host Groups|Macros|
|----|---------|-----------|------|
|`{#NAME}`|[Object First OOTBI Host by HTTP](#template-object-first-ootbi-host-by-http)|Object First|<ul><li>`{$OOTBI.HOST.ID}`</li><li>{#ID}</li><li>The OOTBI Host ID. This is discovered automatically</li></ul>|

## Template: Object First OOTBI Host by HTTP

This template is designed to monitor an Object First OOTBI Host and will automatically be attached to hosts discovered from your cluster.

### Screenshots for Object First OOTBI Host by HTTP

![Items](./media/host_items.png)

![Triggers](./media/host_triggers.png)

![Discovery](./media/host_discovery.png)

### Macros for Object First OOTBI Host by HTTP

|Name|Description|Default|
|----|-----------|-------|
|{$OOTBI.API.URL}|The OOTBI Cluster endpoint is a URL or IP in the format `<scheme>://<host>:<port>`. This needs to be the pointing to the Service Point of your OOTBI Cluster|Inherited from Object First OOTBI Cluster by HTTP, customizable|
|{$OOTBI.CLUSTER.ID}|The OOTBI Cluster ID (UUID). The Cluster UUID can be found in Settings. Optional, defaults to first cluster.|Inherited from Object First OOTBI Cluster by HTTP, customizable|
|{$OOTBI.HTTP.PROXY}|Sets the HTTP proxy to `http_proxy` value. If this parameter is empty, then no proxy is used.|Inherited from Object First OOTBI Cluster by HTTP, customizable|
|{$OOTBI.PASSWORD}|The `password` of the Object First OOTBI Cluster account. It is used to obtain an access token.|Inherited from Object First OOTBI Cluster by HTTP, customizable|
|{$OOTBI.USER}|The `username` of the Object First OOTBI Cluster account. It is used to obtain an access token.|Inherited from Object First OOTBI Cluster by HTTP, customizable|
|{$OOTBI.DATA.TIMEOUT}|A response timeout for the API.|Inherited from Object First OOTBI Cluster by HTTP, customizable|
|{$OOTBI.HOST.ID}|The OOTBI Host ID. This is discovered automatically|Set by Object First OOTBI Cluster by HTTP, customizable|

### Items for Object First OOTBI Host by HTTP

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get metrics|The result of API requests is expressed in the JSON.|Script|ootbi.get.metrics|
|Get errors|The errors from API requests.|Dependent item|ootbi.get.errors<br />**Preprocessing**<ul><li>JSON Path: `$.error`⛔️Custom on fail: Set value to</li><li>Discard unchanged with heartbeat: `1h`</li></ul>|
|Host Status|Get the host status.|Dependent item|ootbi.get.host.status<br />**Preprocessing**<ul><li>JSON Path: `$.hosts.members.[?(@.id=='{$OOTBI.HOST.ID}')].status.first()`</li><li>Discard unchanged with heartbeat: `1h`</li></ul>|
|Global Disks Status|Get the global disk status|Dependent item|ootbi.get.disk.status<br />**Preprocessing**<ul><li>JSON Path: `$.disks.status`</li><li>Discard unchanged with heartbeat: `1h`</li></ul>|
|Uptime|The uptime of the node.|Dependent item|ootbi.get.host.uptime<br />**Preprocessing**<ul><li>JSON Path: `$.hosts.members.[?(@.id=='{$OOTBI.HOST.ID}')].uptimeInSec.first()`</li></ul>|

### Triggers for Object First OOTBI Host by HTTP

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Object First OOTBI: Node restarted (uptime < 10m)|<p>The cluster node's uptime is less than 10 minutes.</p>|`last(/Object First OOTBI Host by HTTP/ootbi.get.host.uptime)<10`|Average||
|Object First OOTBI Disks not healthy|<p>The host reports its disks are healthy. Please inspect the host.</p>|`find(/Object First OOTBI Host by HTTP/ootbi.get.disk.status,,"like","ok")=0`|High||
|Object First OOTBI Host is not healthy|<p>The host reports its not healthy. Please inspect the host.</p>|`find(/Object First OOTBI Host by HTTP/ootbi.get.host.status,,"like","Ok")=0`|High||
|Object First OOTBI: There are errors in requests to API|<p>Zabbix has received errors in response to API requests.</p>|`length(last(/Object First OOTBI Host by HTTP/ootbi.get.errors))>0`|Average||

### LLD rule Disk Discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Disk Discovery|<p>Discovery of node disks.</p>|Dependent item|ootbi.host.disk.discovery<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.disks.disks`</p></li><li><p>Discard unchanged with heartbeat: `6h`</p></li></ul>|

### Item prototypes for Disk Discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Disk [{#SLOT}]: Get data|<p>Gets raw data from the disk in slot `[{#SLOT}]`.</p>|Dependent item|ootbi.disk.raw[{#ID}]<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.disks.disks.[?(@.id=='{#ID}')].first()`</p></li></ul>|
|Disk Status|<p>The status of the disk.</p>|Dependent item|ootbi.disk.status[{#SLOT}]<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.status`</p></li></ul>|

### Trigger prototypes for Disk Discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Object First OOTBI: Disk in slot {#SLOT} not ok||`find(/Object First OOTBI Host by HTTP/ootbi.disk.status[{#SLOT}],,"like","ok")=0`|Average|**Manual close**: Yes|

## Feedback

Please report any issues with the template by opening an [Issue](https://github.com/mkevenaar/ObjectFirst.Zabbix/issues) on GitHub
