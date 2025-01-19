# Object First OOTBI Cluster by HTTP

## Overview

This template is designed to monitor an Object First OOTBI Cluster.

## Requirements

Zabbix version: 7.0 and higher.

## Tested versions

This template has been tested on Object First OOTBI 1.5.54.10104 (VSA).

## Setup

1. Create a user to monitor the service or use an existing user.
1. Link the template to a host.
1. Configure the following macros: {$OOTBI.API.URL}, {$OOTBI.USER}, and {$OOTBI.PASSWORD}.

### Macros used

|Name|Description|Default|
|----|-----------|-------|
|{$OOTBI.API.URL}|The OOTBI Cluster endpoint is a URL in the format `<scheme>://<host>:<port>`.|`https://localhost:8443`|
|{$OOTBI.HTTP.PROXY}|Sets the HTTP proxy to `http_proxy` value. If this parameter is empty, then no proxy is used.||
|{$OOTBI.PASSWORD}|The `password` of the Object First OOTBI Cluster account. It is used to obtain an access token.||
|{$OOTBI.USER}|The `username` of the Object First OOTBI Cluster account. It is used to obtain an access token.||
|{$OOTBI.DATA.TIMEOUT}|A response timeout for the API.|`10`|

### Items

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Get metrics|The result of API requests is expressed in the JSON.|Script|ootbi.get.metrics|
|Get errors|The errors from API requests.|Dependent item|ootbi.get.errors<br />**Preprocessing**<ul><li>JSON Path: `$.error`⛔️Custom on fail: Set value to</li><li>Discard unchanged with heartbeat: `1h`</li></ul>|
|Cluster Status|Get the cluster status|Dependent item|ootbi.get.cluster.status<br />**Preprocessing**<ul><li>JSON Path: `$.clusters.members[:].status`</li><li>Discard unchanged with heartbeat: `1h`</li></ul>|

### Triggers

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Object First OOTBI: Cluster is not healthy|<p>Cluster reports it's not healthy. Please inspect the cluster.</p>|`find(/Object First OOTBI Cluster by HTTP/ootbi.get.cluster.status,,"like","\"OK\"")=0`|High||
|Object First OOTBI: There are errors in requests to API|<p>Zabbix has received errors in response to API requests.</p>|`length(last(/Object First OOTBI Cluster by HTTP/ootbi.get.errors))>0`|Average||

### LLD rule Node discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Node discovery|<p>Discovery of cluster nodes.</p>|Dependent item|ootbi.node.discovery<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.clusters.members[:].nodes[:]`</p></li><li><p>Discard unchanged with heartbeat: `6h`</p></li></ul>|

### Item prototypes for Node discovery

|Name|Description|Type|Key and additional info|
|----|-----------|----|-----------------------|
|Node [{#NAME}]: Get data|<p>Gets raw data from the node `[{#NAME}]`.</p>|Dependent item|ootbi.node.raw[{#ID}]<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.clusters.members[:].nodes.[?(@.id=='{#ID}')].first()`</p></li></ul>|
|Node [{#NAME}]: Host Name|<p>The name of the cluster node.</p>|Dependent item|ootbi.node.name[{#NAME}]<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.name`</p></li></ul>|
|Node [{#NAME}]: State|<p>The state of the cluster node.</p>|Dependent item|ootbi.node.state[{#NAME}]<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.state`</p></li></ul>|
|Node [{#NAME}]: Uptime|<p>The uptime of the cluster node.</p>|Dependent item|ootbi.node.uptime[{#NAME}]<p>**Preprocessing**</p><ul><li><p>JSON Path: `$.uptimeInSec`</p></li></ul>|

### Trigger prototypes for Node discovery

|Name|Description|Expression|Severity|Dependencies and additional info|
|----|-----------|----------|--------|--------------------------------|
|Object First OOTBI: {#NAME} Cluster node not healthy||`find(/Object First OOTBI Cluster by HTTP/ootbi.node.state[{#NAME}],,"like","OK")=0`|Average|**Manual close**: Yes|
|Object First OOTBI: {#NAME} Cluster node restarted (uptime < 10m)||`last(/Object First OOTBI Cluster by HTTP/ootbi.node.uptime[{#NAME}])<10m`|Average|**Manual close**: Yes|

## Feedback

Please report any issues with the template by opening an [Issue](https://github.com/mkevenaar/ObjectFirst.Zabbix/issues) on GitHub
