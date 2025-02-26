# Zscaler ZIA

This integration is for Zscaler Internet Access logs. It can be used
to receive logs sent by NSS log server on respective TCP ports.

The log message is expected to be in JSON format. The data is mapped to
ECS fields where applicable and the remaining fields are written under
`zscaler_zia.<data-stream-name>.*`.

## Setup steps

1. Enable the integration with the TCP input.
2. Configure the Zscaler NSS Server and NSS Feeds to send logs to the Elastic Agent
that is running this integration. See [_Add NSS Server_](https://help.zscaler.com/zia/adding-nss-servers) and [_Add NSS Feeds_](https://help.zscaler.com/zia/adding-nss-feeds). Use the IP address/hostname of the Elastic Agent as the 'NSS Feed SIEM IP Address/FQDN', and use the listening port of the Elastic Agent as the 'SIEM TCP Port' on the _Add NSS Feed_ configuration screen.
3. *Please make sure to use the given response formats.*

## Compatibility

This package has been tested against `Zscaler Internet Access version 6.1`

## Documentation and configuration

### Alerts

Default port: _9010_

Vendor documentation: https://help.zscaler.com/zia/about-alerts

Zscaler response format:  
```
<%d{syslogid}>%s{Monthname} %2d{Dayofmonth} %02d{Hour}:%02d{Minutes}:%02d{Seconds} [%s{Deviceip}] ZscalerNSS: %s{Eventinfo}\n
```

Sample Response: 
```
<114>Dec 10 14:04:28 [175.16.199.1] ZscalerNSS: Zscaler cloud configuration connection to  175.16.199.1:443 lost and unavailable for the past 2325.00 minutes
```

### DNS Log

Default port: _9011_

Vendor documentation: https://help.zscaler.com/zia/nss-feed-output-format-dns-logs

Zscaler response format:  
```
\{ "sourcetype" : "zscalernss-dns", "event" :\{"datetime":"%s{time}","user":"%s{elogin}","department":"%s{edepartment}","location":"%s{elocation}","reqaction":"%s{reqaction}","resaction":"%s{resaction}","reqrulelabel":"%s{reqrulelabel}","resrulelabel":"%s{resrulelabel}","dns_reqtype":"%s{reqtype}","dns_req":"%s{req}","dns_resp":"%s{res}","srv_dport":"%d{sport}","durationms":"%d{durationms}","clt_sip":"%s{cip}","srv_dip":"%s{sip}","category":"%s{domcat}","deviceowner":"%s{deviceowner}","devicehostname":"%s{devicehostname}"\}\}
```

Sample Response: 
```json
{ "sourcetype" : "zscalernss-dns", "event" :{"datetime":"Fri Dec 17 07:27:54 2021","user":"some_user@example.com","department":"Unknown","location":"TestLoc%20DB","reqaction":"REQ_ALLOW","resaction":"Some Response Action","reqrulelabel":"Access%20Blocked","resrulelabel":"None","dns_reqtype":"Some type","dns_req":"example.com","dns_resp":"Some response string","srv_dport":"8080","durationms":"123456","clt_sip":"81.2.69.193","srv_dip":"81.2.69.144","category":"Professional Services","deviceowner":"Owner77","devicehostname":"Machine9000"}}
```

### Firewall Log

Default port: _9012_

Vendor documentation: https://help.zscaler.com/zia/nss-feed-output-format-firewall-logs

Zscaler response format:  
```
\{ "sourcetype" : "zscalernss-fw", "event" :\{"datetime":"%s{time}","user":"%s{elogin}","department":"%s{edepartment}","locationname":"%s{elocation}","cdport":"%d{cdport}","csport":"%d{csport}","sdport":"%d{sdport}","ssport":"%d{ssport}","csip":"%s{csip}","cdip":"%s{cdip}","ssip":"%s{ssip}","sdip":"%s{sdip}","tsip":"%s{tsip}","tunsport":"%d{tsport}","tuntype":"%s{ttype}","action":"%s{action}","dnat":"%s{dnat}","stateful":"%s{stateful}","aggregate":"%s{aggregate}","nwsvc":"%s{nwsvc}","nwapp":"%s{nwapp}","proto":"%s{ipproto}","ipcat":"%s{ipcat}","destcountry":"%s{destcountry}","avgduration":"%d{avgduration}","rulelabel":"%s{erulelabel}","inbytes":"%ld{inbytes}","outbytes":"%ld{outbytes}","duration":"%d{duration}","durationms":"%d{durationms}","numsessions":"%d{numsessions}","ipsrulelabel":"%s{ipsrulelabel}","threatcat":"%s{threatcat}","threatname":"%s{ethreatname}","deviceowner":"%s{deviceowner}","devicehostname":"%s{devicehostname}"\}\}
```

Sample Response: 
```json
{ "sourcetype" : "zscalernss-fw", "event" :{"datetime":"Fri Dec 17 07:27:54 2021","user":"some_user@example.com","department":"Unknown","locationname":"TestLoc%20DB","cdport":443,"csport":55018,"sdport":443,"ssport":0,"csip":"0.0.0.0","cdip":"0.0.0.0","ssip":"0.0.0.0","sdip":"0.0.0.0","tsip":"0.0.0.0","tunsport":0,"tuntype":"ZscalerClientConnector","action":"Drop","dnat":"No","stateful":"Yes","aggregate":"No","nwsvc":"HTTPS","nwapp":"http","proto":"TCP","ipcat":"Test Name","destcountry":"Ireland","avgduration":486,"rulelabel":"Access%20Blocked","inbytes":19052,"outbytes":1734,"duration":0,"durationms":486,"numsessions":1,"ipsrulelabel":"None","threatcat":"None","threatname":"None","deviceowner":"admin77","devicehostname":"Machine9000"}}
```

### Tunnel Log

Default port: _9013_

Vendor documentation: https://help.zscaler.com/zia/nss-feed-output-format-tunnel-logs

Zscaler response format:
- Tunnel Event:
    ```
    \{ "sourcetype" : "zscalernss-tunnel", "event" : \{"datetime":"%s{datetime}","Recordtype":"%s{tunnelactionname}","tunneltype":"%s{tunneltype}","user":"%s{vpncredentialname}","location":"%s{elocationname}","sourceip":"%s{sourceip}","destinationip":"%s{destvip}","sourceport":"%d{srcport}","event":"%s{event}","eventreason":"%s{eventreason}","recordid":"%d{recordid}"\}\}
    ```
- Sample Event:
    ```
    \{ "sourcetype" : "zscalernss-tunnel", "event" : \{"datetime":"%s{datetime}","Recordtype":"%s{tunnelactionname}","tunneltype":"%s{tunneltype}","user":"%s{vpncredentialname}","location":"%s{elocationname}","sourceip":"%s{sourceip}","destinationip":"%s{destvip}","sourceport":"%d{srcport}","txbytes":"%lu{txbytes}","rxbytes":"%lu{rxbytes}","dpdrec":"%d{dpdrec}","recordid":"%d{recordid}"\}\}
    ```
- IKE Phase 1
    ```
    \{ "sourcetype" : "zscalernss-tunnel", "event" : \{"datetime":"%s{datetime}","Recordtype":"%s{tunnelactionname}","tunneltype":"IPSEC IKEV %d{ikeversion}","user":"%s{vpncredentialname}","location":"%s{elocationname}","sourceip":"%s{sourceip}","destinationip":"%s{destvip}","sourceport":"%d{srcport}","destinationport":"%d{dstport}","lifetime":"%d{lifetime}","ikeversion":"%d{ikeversion}","spi_in":"%lu{spi_in}","spi_out":"%lu{spi_out}","algo":"%s{algo}","authentication":"%s{authentication}","authtype":"%s{authtype}","recordid":"%d{recordid}"\}\}
    ```
- IKE Phase 2
    ```
    \{ "sourcetype" : "zscalernss-tunnel", "event" : \{"datetime":"%s{datetime}","Recordtype":"%s{tunnelactionname}","tunneltype":"IPSEC IKEV %d{ikeversion}","user":"%s{vpncredentialname}","location":"%s{elocationname}","sourceip":"%s{sourceip}","destinationip":"%s{destvip}","sourceport":"%d{srcport}","sourceportstart":"%d{srcportstart}","destinationportstart":"%d{destportstart}","srcipstart":"%s{srcipstart}","srcipend":"%s{srcipend}","destinationipstart":"%s{destipstart}","destinationipend":"%s{destipend}","lifetime":"%d{lifetime}","ikeversion":"%d{ikeversion}","lifebytes":"%d{lifebytes}","spi":"%d{spi}","algo":"%s{algo}","authentication":"%s{authentication}","authtype":"%s{authtype}","protocol":"%s{protocol}","tunnelprotocol":"%s{tunnelprotocol}","policydirection":"%s{policydirection}","recordid":"%d{recordid}"\}\}
    ```

Sample Response: 
```json
{ "sourcetype" : "zscalernss-tunnel", "event" : {"datetime":"Thu Dec 30 11:40:27 2021","Recordtype":"IPSec Phase1","tunneltype":"IPSEC IKEV 2","user":"81.2.69.145","location":"some-location","sourceip":"81.2.69.145","destinationip":"81.2.69.143","sourceport":"500","destinationport":"500","lifetime":"0","ikeversion":"2","spi_in":"00000000000000000000","spi_out":"11111111111111111111","algo":"AES-CBS","authentication":"HMAC-SHA1-96","authtype":"PSK","recordid":"1111111111111111111"}}
```

### Web Log

Default port: _9014_

Vendor documentation: https://help.zscaler.com/zia/nss-feed-output-format-web-logs

Zscaler response format:  
```
\{ "sourcetype" : "zscalernss-web", "event" :\{"time":"%s{time}","login":"%s{login}","proto":"%s{proto}","eurl":"%s{eurl}","action":"%s{action}","appname":"%s{appname}","appclass":"%s{appclass}","reqsize":"%d{reqsize}","respsize":"%d{respsize}","stime":"%d{stime}","ctime":"%d{ctime}","urlclass":"%s{urlclass}","urlsupercat":"%s{urlsupercat}","urlcat":"%s{urlcat}","malwarecat":"%s{malwarecat}","threatname":"%s{threatname}","riskscore":"%d{riskscore}","dlpeng":"%s{dlpeng}","dlpdict":"%s{dlpdict}","location":"%s{location}","dept":"%s{dept}","cip":"%s{cip}","sip":"%s{sip}","reqmethod":"%s{reqmethod}","respcode":"%s{respcode}","ua":"%s{ua}","ereferer":"%s{ereferer}","ruletype":"%s{ruletype}","rulelabel":"%s{rulelabel}","contenttype":"%s{contenttype}","unscannabletype":"%s{unscannabletype}","deviceowner":"%s{deviceowner}","devicehostname":"%s{devicehostname}"\}\}
```

Sample Response: 
```json
{ "sourcetype" : "zscalernss-web", "event" :{"time":"Fri Dec 17 07:04:57 2021","login":"test@example.com","proto":"HTTP_PROXY","eurl":"browser.events.data.msn.com:443","action":"Blocked","appname":"General Browsing","appclass":"General Browsing","reqsize":"600","respsize":"65","stime":"0","ctime":"0","urlclass":"Business Use","urlsupercat":"Information Technology","urlcat":"Web Search","malwarecat":"None","threatname":"None","riskscore":"0","dlpeng":"None","dlpdict":"None","location":"Test DB","dept":"Unknown","cip":"81.2.69.193","sip":"81.2.69.145","reqmethod":"CONNECT","respcode":"200","ua":"Windows Microsoft Windows 10 Pro ZTunnel/1.0","ereferer":"None","ruletype":"FwFilter","rulelabel":"Zscaler Proxy Traffic","contenttype":"Other","unscannabletype":"None","deviceowner":"administrator1","devicehostname":"TestMachine35"}}
```

## Fields and Sample event

### Alerts

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| cloud.account.id | The cloud account or organization id used to identify different entities in a multi-tenant environment. Examples: AWS account id, Google Cloud ORG Id, or other unique identifier. | keyword |
| cloud.availability_zone | Availability zone in which this host is running. | keyword |
| cloud.image.id | Image ID for the cloud instance. | keyword |
| cloud.instance.id | Instance ID of the host machine. | keyword |
| cloud.instance.name | Instance name of the host machine. | keyword |
| cloud.machine.type | Machine type of the host machine. | keyword |
| cloud.project.id | Name of the project in Google Cloud. | keyword |
| cloud.provider | Name of the cloud provider. Example values are aws, azure, gcp, or digitalocean. | keyword |
| cloud.region | Region in which this host is running. | keyword |
| container.id | Unique container id. | keyword |
| container.image.name | Name of the image the container was built on. | keyword |
| container.labels | Image labels. | object |
| container.name | Container name. | keyword |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| destination.address | Some event destination addresses are defined ambiguously. The event will sometimes list an IP, a domain or a unix socket.  You should always store the raw address in the `.address` field. Then it should be duplicated to `.ip` or `.domain`, depending on which one it is. | keyword |
| destination.ip | IP address of the destination (IPv4 or IPv6). | ip |
| destination.port | Port of the destination. | long |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |
| event.dataset | Event dataset | constant_keyword |
| event.module | Event module | constant_keyword |
| host.architecture | Operating system architecture. | keyword |
| host.containerized | If the host is a container. | boolean |
| host.domain | Name of the domain of which the host is a member. For example, on Windows this could be the host's Active Directory domain or NetBIOS domain name. For Linux this could be the domain of the host's LDAP provider. | keyword |
| host.hostname | Hostname of the host. It normally contains what the `hostname` command returns on the host machine. | keyword |
| host.id | Unique host id. As hostname is not always unique, use values that are meaningful in your environment. Example: The current usage of `beat.name`. | keyword |
| host.ip | Host ip addresses. | ip |
| host.mac | Host mac addresses. | keyword |
| host.name | Name of the host. It can contain what `hostname` returns on Unix systems, the fully qualified domain name, or a name specified by the user. The sender decides which value to use. | keyword |
| host.os.build | OS build information. | keyword |
| host.os.codename | OS codename, if any. | keyword |
| host.os.family | OS family (such as redhat, debian, freebsd, windows). | keyword |
| host.os.kernel | Operating system kernel version as a raw string. | keyword |
| host.os.name | Operating system name, without the version. | keyword |
| host.os.platform | Operating system platform (such centos, ubuntu, windows). | keyword |
| host.os.version | Operating system version as a raw string. | keyword |
| host.type | Type of host. For Cloud providers this can be the machine type like `t2.medium`. If vm, this could be the container, for example, or other information meaningful in your environment. | keyword |
| input.type | Input type | keyword |
| log.offset | Log offset | long |
| log.source.address | Source address from which the log event was read / sent from. | keyword |
| log.syslog.priority | Syslog numeric priority of the event, if available. According to RFCs 5424 and 3164, the priority is 8 \* facility + severity. This number is therefore expected to contain a value between 0 and 191. | long |
| message | For log events the message field contains the log message, optimized for viewing in a log viewer. For structured logs without an original message field, other fields can be concatenated to form a human-readable summary of the event. If multiple messages exist, they can be combined into one message. | match_only_text |
| related.ip | All of the IPs seen on your event. | ip |
| tags | List of keywords used to tag each event. | keyword |
| zscaler_zia.alerts.connection_lost_minutes | Amount of time after loosing connection to a server in Minutes. | double |
| zscaler_zia.alerts.log_feed_name | Name of the NSS log feed. | keyword |


An example event for `alerts` looks as following:

```json
{
    "@timestamp": "2022-12-10T13:40:32.000Z",
    "agent": {
        "ephemeral_id": "8c093fcf-fb2f-4baa-b794-40edb011194d",
        "hostname": "docker-fleet-agent",
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "name": "docker-fleet-agent",
        "type": "filebeat",
        "version": "7.16.2"
    },
    "data_stream": {
        "dataset": "zscaler_zia.alerts",
        "namespace": "ep",
        "type": "logs"
    },
    "destination": {
        "address": "81.2.69.193",
        "ip": "81.2.69.193",
        "port": 9012
    },
    "ecs": {
        "version": "8.0.0"
    },
    "elastic_agent": {
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "snapshot": false,
        "version": "7.16.2"
    },
    "event": {
        "agent_id_status": "verified",
        "dataset": "zscaler_zia.alerts",
        "ingested": "2022-02-04T06:31:25Z"
    },
    "input": {
        "type": "tcp"
    },
    "log": {
        "source": {
            "address": "172.21.0.7:32902"
        },
        "syslog": {
            "priority": 114
        }
    },
    "message": "ZscalerNSS: SIEM Feed connection \"DNS Logs Feed\" to 81.2.69.193:9012 lost and unavailable for the past 2440.00 minutes",
    "related": {
        "ip": [
            "81.2.69.193"
        ]
    },
    "tags": [
        "forwarded",
        "zscaler_zia-alerts"
    ],
    "zscaler_zia": {
        "alerts": {
            "connection_lost_minutes": 2440,
            "log_feed_name": "DNS Logs Feed"
        }
    }
}
```

## DNS Logs

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| client.geo.city_name | City name. | keyword |
| client.geo.continent_name | Name of the continent. | keyword |
| client.geo.country_iso_code | Country ISO code. | keyword |
| client.geo.country_name | Country name. | keyword |
| client.geo.location | Longitude and latitude. | geo_point |
| client.geo.region_iso_code | Region ISO code. | keyword |
| client.geo.region_name | Region name. | keyword |
| client.ip | IP address of the client (IPv4 or IPv6). | ip |
| cloud.account.id | The cloud account or organization id used to identify different entities in a multi-tenant environment. Examples: AWS account id, Google Cloud ORG Id, or other unique identifier. | keyword |
| cloud.availability_zone | Availability zone in which this host is running. | keyword |
| cloud.image.id | Image ID for the cloud instance. | keyword |
| cloud.instance.id | Instance ID of the host machine. | keyword |
| cloud.instance.name | Instance name of the host machine. | keyword |
| cloud.machine.type | Machine type of the host machine. | keyword |
| cloud.project.id | Name of the project in Google Cloud. | keyword |
| cloud.provider | Name of the cloud provider. Example values are aws, azure, gcp, or digitalocean. | keyword |
| cloud.region | Region in which this host is running. | keyword |
| container.id | Unique container id. | keyword |
| container.image.name | Name of the image the container was built on. | keyword |
| container.labels | Image labels. | object |
| container.name | Container name. | keyword |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| dns.answers.name | The domain name to which this resource record pertains. If a chain of CNAME is being resolved, each answer's `name` should be the one that corresponds with the answer's `data`. It should not simply be the original `question.name` repeated. | keyword |
| dns.question.name | The name being queried. If the name field contains non-printable characters (below 32 or above 126), those characters should be represented as escaped base 10 integers (\DDD). Back slashes and quotes should be escaped. Tabs, carriage returns, and line feeds should be converted to \t, \r, and \n respectively. | keyword |
| dns.question.type | The type of record being queried. | keyword |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |
| event.dataset | Event dataset | constant_keyword |
| event.module | Event module | constant_keyword |
| host.architecture | Operating system architecture. | keyword |
| host.containerized | If the host is a container. | boolean |
| host.domain | Name of the domain of which the host is a member. For example, on Windows this could be the host's Active Directory domain or NetBIOS domain name. For Linux this could be the domain of the host's LDAP provider. | keyword |
| host.hostname | Hostname of the host. It normally contains what the `hostname` command returns on the host machine. | keyword |
| host.id | Unique host id. As hostname is not always unique, use values that are meaningful in your environment. Example: The current usage of `beat.name`. | keyword |
| host.ip | Host ip addresses. | ip |
| host.mac | Host mac addresses. | keyword |
| host.name | Name of the host. It can contain what `hostname` returns on Unix systems, the fully qualified domain name, or a name specified by the user. The sender decides which value to use. | keyword |
| host.os.build | OS build information. | keyword |
| host.os.codename | OS codename, if any. | keyword |
| host.os.family | OS family (such as redhat, debian, freebsd, windows). | keyword |
| host.os.kernel | Operating system kernel version as a raw string. | keyword |
| host.os.name | Operating system name, without the version. | keyword |
| host.os.platform | Operating system platform (such centos, ubuntu, windows). | keyword |
| host.os.version | Operating system version as a raw string. | keyword |
| host.type | Type of host. For Cloud providers this can be the machine type like `t2.medium`. If vm, this could be the container, for example, or other information meaningful in your environment. | keyword |
| input.type | Input type | keyword |
| log.offset | Log offset | long |
| log.source.address | Source address from which the log event was read / sent from. | keyword |
| related.hosts | All hostnames or other host identifiers seen on your event. Example identifiers include FQDNs, domain names, workstation names, or aliases. | keyword |
| related.ip | All of the IPs seen on your event. | ip |
| server.geo.city_name | City name. | keyword |
| server.geo.continent_name | Name of the continent. | keyword |
| server.geo.country_iso_code | Country ISO code. | keyword |
| server.geo.country_name | Country name. | keyword |
| server.geo.location | Longitude and latitude. | geo_point |
| server.geo.region_iso_code | Region ISO code. | keyword |
| server.geo.region_name | Region name. | keyword |
| server.ip | IP address of the server (IPv4 or IPv6). | ip |
| server.port | Port of the server. | long |
| source.bytes | Bytes sent from the source to the destination. | long |
| source.ip | IP address of the source (IPv4 or IPv6). | ip |
| source.packets | Packets sent from the source to the destination. | long |
| source.port | Port of the source. | long |
| tags | List of keywords used to tag each event. | keyword |
| user.email | User email address. | keyword |
| user.name | Short name or login of the user. | keyword |
| zscaler_zia.dns.department | Department of the user. | keyword |
| zscaler_zia.dns.dom.category | URL Category of the FQDN in the DNS request. | keyword |
| zscaler_zia.dns.duration.milliseconds | Duration of the DNS request in milliseconds. | long |
| zscaler_zia.dns.hostname | N/A | keyword |
| zscaler_zia.dns.location | Gateway location or sub-location of the source. | keyword |
| zscaler_zia.dns.request.action | Name of the action that was applied to the DNS request. | keyword |
| zscaler_zia.dns.request.rule.label | Name of the rule that was applied to the DNS request. | keyword |
| zscaler_zia.dns.response.action | Name of the action that was applied to the DNS response. | keyword |
| zscaler_zia.dns.response.rule.label | Name of the rule that was applied to the DNS response. | keyword |


An example event for `dns` looks as following:

```json
{
    "@timestamp": "2021-12-17T07:27:54.000Z",
    "agent": {
        "ephemeral_id": "d288c261-b8db-45af-99c0-a673c3c6d8e1",
        "hostname": "docker-fleet-agent",
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "name": "docker-fleet-agent",
        "type": "filebeat",
        "version": "7.16.2"
    },
    "client": {
        "geo": {
            "city_name": "London",
            "continent_name": "Europe",
            "country_iso_code": "GB",
            "country_name": "United Kingdom",
            "location": {
                "lat": 51.5142,
                "lon": -0.0931
            },
            "region_iso_code": "GB-ENG",
            "region_name": "England"
        },
        "ip": "81.2.69.193"
    },
    "data_stream": {
        "dataset": "zscaler_zia.dns",
        "namespace": "ep",
        "type": "logs"
    },
    "dns": {
        "answers": {
            "name": "Some response string"
        },
        "question": {
            "name": "example.com",
            "type": "Some type"
        }
    },
    "ecs": {
        "version": "8.0.0"
    },
    "elastic_agent": {
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "snapshot": false,
        "version": "7.16.2"
    },
    "event": {
        "agent_id_status": "verified",
        "category": "network",
        "dataset": "zscaler_zia.dns",
        "ingested": "2022-02-04T06:32:56Z",
        "kind": "event",
        "type": [
            "info"
        ]
    },
    "input": {
        "type": "tcp"
    },
    "log": {
        "source": {
            "address": "172.21.0.7:54202"
        }
    },
    "related": {
        "hosts": [
            "Machine9000"
        ],
        "ip": [
            "81.2.69.193",
            "81.2.69.144"
        ]
    },
    "server": {
        "geo": {
            "city_name": "London",
            "continent_name": "Europe",
            "country_iso_code": "GB",
            "country_name": "United Kingdom",
            "location": {
                "lat": 51.5142,
                "lon": -0.0931
            },
            "region_iso_code": "GB-ENG",
            "region_name": "England"
        },
        "ip": "81.2.69.144",
        "port": 8080
    },
    "tags": [
        "forwarded",
        "zscaler_zia-dns"
    ],
    "user": {
        "email": "some_user@example.com",
        "name": "Owner77"
    },
    "zscaler_zia": {
        "dns": {
            "department": "Unknown",
            "dom": {
                "category": "Professional Services"
            },
            "duration": {
                "milliseconds": 123456
            },
            "hostname": "Machine9000",
            "location": "TestLoc DB",
            "request": {
                "action": "REQ_ALLOW",
                "rule": {
                    "label": "Access Blocked"
                }
            },
            "response": {
                "action": "Some Response Action",
                "rule": {
                    "label": "None"
                }
            }
        }
    }
}
```

## Firewall Logs

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| client.bytes | Bytes sent from the client to the server. | long |
| cloud.account.id | The cloud account or organization id used to identify different entities in a multi-tenant environment. Examples: AWS account id, Google Cloud ORG Id, or other unique identifier. | keyword |
| cloud.availability_zone | Availability zone in which this host is running. | keyword |
| cloud.image.id | Image ID for the cloud instance. | keyword |
| cloud.instance.id | Instance ID of the host machine. | keyword |
| cloud.instance.name | Instance name of the host machine. | keyword |
| cloud.machine.type | Machine type of the host machine. | keyword |
| cloud.project.id | Name of the project in Google Cloud. | keyword |
| cloud.provider | Name of the cloud provider. Example values are aws, azure, gcp, or digitalocean. | keyword |
| cloud.region | Region in which this host is running. | keyword |
| container.id | Unique container id. | keyword |
| container.image.name | Name of the image the container was built on. | keyword |
| container.labels | Image labels. | object |
| container.name | Container name. | keyword |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |
| event.action | The action captured by the event. This describes the information in the event. It is more specific than `event.category`. Examples are `group-add`, `process-started`, `file-created`. The value is normally defined by the implementer. | keyword |
| event.dataset | Event dataset | constant_keyword |
| event.module | Event module | constant_keyword |
| host.architecture | Operating system architecture. | keyword |
| host.containerized | If the host is a container. | boolean |
| host.domain | Name of the domain of which the host is a member. For example, on Windows this could be the host's Active Directory domain or NetBIOS domain name. For Linux this could be the domain of the host's LDAP provider. | keyword |
| host.hostname | Hostname of the host. It normally contains what the `hostname` command returns on the host machine. | keyword |
| host.id | Unique host id. As hostname is not always unique, use values that are meaningful in your environment. Example: The current usage of `beat.name`. | keyword |
| host.ip | Host ip addresses. | ip |
| host.mac | Host mac addresses. | keyword |
| host.name | Name of the host. It can contain what `hostname` returns on Unix systems, the fully qualified domain name, or a name specified by the user. The sender decides which value to use. | keyword |
| host.os.build | OS build information. | keyword |
| host.os.codename | OS codename, if any. | keyword |
| host.os.family | OS family (such as redhat, debian, freebsd, windows). | keyword |
| host.os.kernel | Operating system kernel version as a raw string. | keyword |
| host.os.name | Operating system name, without the version. | keyword |
| host.os.platform | Operating system platform (such centos, ubuntu, windows). | keyword |
| host.os.version | Operating system version as a raw string. | keyword |
| host.type | Type of host. For Cloud providers this can be the machine type like `t2.medium`. If vm, this could be the container, for example, or other information meaningful in your environment. | keyword |
| input.type | Input type | keyword |
| log.offset | Log offset | long |
| log.source.address | Source address from which the log event was read / sent from. | keyword |
| network.application | When a specific application or service is identified from network connection details (source/dest IPs, ports, certificates, or wire format), this field captures the application's or service's name. For example, the original event identifies the network connection being from a specific web service in a `https` network connection, like `facebook` or `twitter`. The field value must be normalized to lowercase for querying. | keyword |
| network.protocol | In the OSI Model this would be the Application Layer protocol. For example, `http`, `dns`, or `ssh`. The field value must be normalized to lowercase for querying. | keyword |
| network.transport | Same as network.iana_number, but instead using the Keyword name of the transport layer (udp, tcp, ipv6-icmp, etc.) The field value must be normalized to lowercase for querying. | keyword |
| related.ip | All of the IPs seen on your event. | ip |
| rule.name | The name of the rule or signature generating the event. | keyword |
| server.bytes | Bytes sent from the server to the client. | long |
| server.geo.country_name | Country name. | keyword |
| tags | List of keywords used to tag each event. | keyword |
| user.email | User email address. | keyword |
| user.name | Short name or login of the user. | keyword |
| zscaler_zia.firewall.aggregate |  | keyword |
| zscaler_zia.firewall.client.destination.ip | Client destination IP address. For aggregated sessions, this is the client destination IP address of the last session in the aggregate. | keyword |
| zscaler_zia.firewall.client.destination.port | Client destination port. For aggregated sessions, this is the client destination port of the last session in the aggregate. | double |
| zscaler_zia.firewall.client.source.ip | Client source IP address. For aggregated sessions, this is the client source IP address of the last session in the aggregate. | keyword |
| zscaler_zia.firewall.client.source.port | Client source port. For aggregated sessions, this is the client source port of the last session in the aggregate. | double |
| zscaler_zia.firewall.department | Department of the user. | keyword |
| zscaler_zia.firewall.duration.avg | Average session duration, in milliseconds, if the sessions were aggregated. | double |
| zscaler_zia.firewall.duration.milliseconds | Session or request duration in milliseconds. | double |
| zscaler_zia.firewall.duration.seconds | Session or request duration in seconds. | double |
| zscaler_zia.firewall.ip_category | URL category that corresponds to the server IP address. | keyword |
| zscaler_zia.firewall.location.name | Name of the location from which the session was initiated. | keyword |
| zscaler_zia.firewall.nat | Indicates if the destination NAT policy was applied. | keyword |
| zscaler_zia.firewall.server.destination.ip | Server Destination IP address. For aggregated sessions, this is the server destination IP address of the last session in the aggregate. | keyword |
| zscaler_zia.firewall.server.destination.port | Server destination port. For aggregated sessions, this is the server destination port of the last session in the aggregate. | double |
| zscaler_zia.firewall.server.source.ip | Server source IP address. For aggregated sessions, this is the server source IP address of the last session in the aggregate. | keyword |
| zscaler_zia.firewall.server.source.port | Server source port. For aggregated sessions, this is the server source port of the last session in the aggregate. | double |
| zscaler_zia.firewall.session.count | Number of sessions that were aggregated. | double |
| zscaler_zia.firewall.stateful |  | keyword |
| zscaler_zia.firewall.threat.category | Category of the threat in the Firewall session by the IPS engine. | keyword |
| zscaler_zia.firewall.threat.name | Name of the threat detected in the Firewall session by the IPS engine. | keyword |
| zscaler_zia.firewall.tunnel.ip | Tunnel IP address of the client (source). For aggregated sessions, this is the client's tunnel IP address corresponding to the last session in the aggregate. | keyword |
| zscaler_zia.firewall.tunnel.port | Tunnel port on the client side. For aggregated sessions, this is the client's tunnel port corresponding to the last session in the aggregate. | double |
| zscaler_zia.firewall.tunnel.type | Traffic forwarding method used to send the traffic to the firewall. | keyword |


An example event for `firewall` looks as following:

```json
{
    "@timestamp": "2021-12-17T07:27:54.000Z",
    "agent": {
        "ephemeral_id": "41987f90-74dc-4b4b-9936-4347028cf558",
        "hostname": "docker-fleet-agent",
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "name": "docker-fleet-agent",
        "type": "filebeat",
        "version": "7.16.2"
    },
    "client": {
        "bytes": 1734
    },
    "data_stream": {
        "dataset": "zscaler_zia.firewall",
        "namespace": "ep",
        "type": "logs"
    },
    "ecs": {
        "version": "8.0.0"
    },
    "elastic_agent": {
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "snapshot": false,
        "version": "7.16.2"
    },
    "event": {
        "action": "drop",
        "agent_id_status": "verified",
        "category": "network",
        "dataset": "zscaler_zia.firewall",
        "ingested": "2022-02-04T06:34:17Z",
        "kind": "event",
        "original": "{ \"sourcetype\" : \"zscalernss-fw\", \"event\" :{\"datetime\":\"Fri Dec 17 07:27:54 2021\",\"user\":\"some_user@example.com\",\"department\":\"Unknown\",\"locationname\":\"TestLoc%20DB\",\"cdport\":443,\"csport\":55018,\"sdport\":443,\"ssport\":0,\"csip\":\"0.0.0.0\",\"cdip\":\"0.0.0.0\",\"ssip\":\"0.0.0.0\",\"sdip\":\"0.0.0.0\",\"tsip\":\"0.0.0.0\",\"tunsport\":0,\"tuntype\":\"ZscalerClientConnector\",\"action\":\"Drop\",\"dnat\":\"No\",\"stateful\":\"Yes\",\"aggregate\":\"No\",\"nwsvc\":\"HTTPS\",\"nwapp\":\"http\",\"proto\":\"TCP\",\"ipcat\":\"Test Name\",\"destcountry\":\"Ireland\",\"avgduration\":486,\"rulelabel\":\"Access%20Blocked\",\"inbytes\":19052,\"outbytes\":1734,\"duration\":0,\"durationms\":486,\"numsessions\":1,\"ipsrulelabel\":\"None\",\"threatcat\":\"None\",\"threatname\":\"None\",\"deviceowner\":\"admin77\",\"devicehostname\":\"Machine9000\"}}",
        "type": "info"
    },
    "host": {
        "hostname": "Machine9000"
    },
    "input": {
        "type": "tcp"
    },
    "log": {
        "source": {
            "address": "172.21.0.7:58194"
        }
    },
    "network": {
        "application": "http",
        "protocol": "https",
        "transport": "tcp"
    },
    "related": {
        "ip": [
            "0.0.0.0"
        ]
    },
    "rule": {
        "name": [
            "Access Blocked",
            "None"
        ]
    },
    "server": {
        "bytes": 19052,
        "geo": {
            "country_name": "Ireland"
        }
    },
    "tags": [
        "forwarded",
        "zscaler_zia-firewall"
    ],
    "user": {
        "email": "some_user@example.com",
        "name": "admin77"
    },
    "zscaler_zia": {
        "firewall": {
            "aggregate": "No",
            "client": {
                "destination": {
                    "ip": "0.0.0.0",
                    "port": 443
                },
                "source": {
                    "ip": "0.0.0.0",
                    "port": 55018
                }
            },
            "department": "Unknown",
            "duration": {
                "avg": 486,
                "milliseconds": 486,
                "seconds": 0
            },
            "ip_category": "Test Name",
            "location": {
                "name": "TestLoc DB"
            },
            "nat": "No",
            "server": {
                "destination": {
                    "ip": "0.0.0.0",
                    "port": 443
                },
                "source": {
                    "ip": "0.0.0.0",
                    "port": 0
                }
            },
            "session": {
                "count": 1
            },
            "stateful": "Yes",
            "threat": {
                "category": "None",
                "name": "None"
            },
            "tunnel": {
                "ip": "0.0.0.0",
                "port": 0,
                "type": "ZscalerClientConnector"
            }
        }
    }
}
```

## Tunnel Logs

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| cloud.account.id | The cloud account or organization id used to identify different entities in a multi-tenant environment. Examples: AWS account id, Google Cloud ORG Id, or other unique identifier. | keyword |
| cloud.availability_zone | Availability zone in which this host is running. | keyword |
| cloud.image.id | Image ID for the cloud instance. | keyword |
| cloud.instance.id | Instance ID of the host machine. | keyword |
| cloud.instance.name | Instance name of the host machine. | keyword |
| cloud.machine.type | Machine type of the host machine. | keyword |
| cloud.project.id | Name of the project in Google Cloud. | keyword |
| cloud.provider | Name of the cloud provider. Example values are aws, azure, gcp, or digitalocean. | keyword |
| cloud.region | Region in which this host is running. | keyword |
| container.id | Unique container id. | keyword |
| container.image.name | Name of the image the container was built on. | keyword |
| container.labels | Image labels. | object |
| container.name | Container name. | keyword |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| destination.bytes | Bytes sent from the destination to the source. | long |
| destination.ip | IP address of the destination (IPv4 or IPv6). | ip |
| destination.packets | Packets sent from the destination to the source. | long |
| destination.port | Port of the destination. | long |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |
| event.action | The action captured by the event. This describes the information in the event. It is more specific than `event.category`. Examples are `group-add`, `process-started`, `file-created`. The value is normally defined by the implementer. | keyword |
| event.dataset | Event dataset | constant_keyword |
| event.id | Unique ID to describe the event. | keyword |
| event.module | Event module | constant_keyword |
| event.reason | Reason why this event happened, according to the source. This describes the why of a particular action or outcome captured in the event. Where `event.action` captures the action from the event, `event.reason` describes why that action was taken. For example, a web proxy with an `event.action` which denied the request may also populate `event.reason` with the reason why (e.g. `blocked site`). | keyword |
| host.architecture | Operating system architecture. | keyword |
| host.containerized | If the host is a container. | boolean |
| host.domain | Name of the domain of which the host is a member. For example, on Windows this could be the host's Active Directory domain or NetBIOS domain name. For Linux this could be the domain of the host's LDAP provider. | keyword |
| host.hostname | Hostname of the host. It normally contains what the `hostname` command returns on the host machine. | keyword |
| host.id | Unique host id. As hostname is not always unique, use values that are meaningful in your environment. Example: The current usage of `beat.name`. | keyword |
| host.ip | Host ip addresses. | ip |
| host.mac | Host mac addresses. | keyword |
| host.name | Name of the host. It can contain what `hostname` returns on Unix systems, the fully qualified domain name, or a name specified by the user. The sender decides which value to use. | keyword |
| host.os.build | OS build information. | keyword |
| host.os.codename | OS codename, if any. | keyword |
| host.os.family | OS family (such as redhat, debian, freebsd, windows). | keyword |
| host.os.kernel | Operating system kernel version as a raw string. | keyword |
| host.os.name | Operating system name, without the version. | keyword |
| host.os.platform | Operating system platform (such centos, ubuntu, windows). | keyword |
| host.os.version | Operating system version as a raw string. | keyword |
| host.type | Type of host. For Cloud providers this can be the machine type like `t2.medium`. If vm, this could be the container, for example, or other information meaningful in your environment. | keyword |
| input.type | Input type | keyword |
| log.offset | Log offset | long |
| log.source.address | Source address from which the log event was read / sent from. | keyword |
| related.ip | All of the IPs seen on your event. | ip |
| related.user | All the user names or other user identifiers seen on the event. | keyword |
| source.bytes | Bytes sent from the source to the destination. | long |
| source.ip | IP address of the source (IPv4 or IPv6). | ip |
| source.packets | Packets sent from the source to the destination. | long |
| source.port | Port of the source. | long |
| tags | List of keywords used to tag each event. | keyword |
| user.name | Short name or login of the user. | keyword |
| zscaler_zia.tunnel.action.type | Type of the record. Possible values [ WL_TUNNEL_IPSECPHASE1, WL_TUNNEL_IPSECPHASE2, WL_TUNNEL_EVENT, WL_TUNNEL_SAMPLES ]. | keyword |
| zscaler_zia.tunnel.authentication.algorithm | Authentication algorithm. | keyword |
| zscaler_zia.tunnel.authentication.type | Authentication type. | keyword |
| zscaler_zia.tunnel.destination.end.ip | Phase 2 policy proposal - Destination IP end. | keyword |
| zscaler_zia.tunnel.destination.start.ip | Phase 2 policy proposal - Destination IP start. | keyword |
| zscaler_zia.tunnel.destination.start.port | Phase 2 policy proposal - Destination port end. | long |
| zscaler_zia.tunnel.dpd_packets | Number of DPD packets received in 60-second sample window. | keyword |
| zscaler_zia.tunnel.encryption.algorithm | Encryption algorithm. | keyword |
| zscaler_zia.tunnel.ike.version | IKE version (1 or 2). | long |
| zscaler_zia.tunnel.life.bytes | Life bytes (number of traffic to be transacted through tunnel before renegotiation). | long |
| zscaler_zia.tunnel.life.time | Lifetime of IKE Phase 1/2 in seconds. | long |
| zscaler_zia.tunnel.location.name | Location name. | keyword |
| zscaler_zia.tunnel.policy.direction | N/A | keyword |
| zscaler_zia.tunnel.policy.protocol | Phase 2 policy proposal - Protocol. | keyword |
| zscaler_zia.tunnel.protocol | IPSec tunnel protocol type (Zscaler only supports ESP). | keyword |
| zscaler_zia.tunnel.source.end.ip | Phase 2 policy proposal - Source IP end. | keyword |
| zscaler_zia.tunnel.source.start.ip | Phase 2 policy proposal - Source IP start. | keyword |
| zscaler_zia.tunnel.source.start.port | Phase 2 policy proposal - Source port start. | long |
| zscaler_zia.tunnel.spi | Security Parameter Index. | keyword |
| zscaler_zia.tunnel.spi_in | Initiator cookie. | keyword |
| zscaler_zia.tunnel.spi_out | Responder cookie. | keyword |
| zscaler_zia.tunnel.type | Tunnel type. | keyword |
| zscaler_zia.tunnel.vendor.name | Vendor name of the edge device. | keyword |


An example event for `tunnel` looks as following:

```json
{
    "@timestamp": "2021-12-30T11:20:12.000Z",
    "agent": {
        "ephemeral_id": "63ac98b6-0ff6-4943-820e-8505eff15937",
        "hostname": "docker-fleet-agent",
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "name": "docker-fleet-agent",
        "type": "filebeat",
        "version": "7.16.2"
    },
    "data_stream": {
        "dataset": "zscaler_zia.tunnel",
        "namespace": "ep",
        "type": "logs"
    },
    "destination": {
        "ip": "81.2.69.143"
    },
    "ecs": {
        "version": "8.0.0"
    },
    "elastic_agent": {
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "snapshot": false,
        "version": "7.16.2"
    },
    "event": {
        "agent_id_status": "verified",
        "category": "network",
        "dataset": "zscaler_zia.tunnel",
        "id": "1111111111111111111",
        "ingested": "2022-02-04T06:36:16Z",
        "kind": "event",
        "type": [
            "info"
        ]
    },
    "input": {
        "type": "tcp"
    },
    "log": {
        "source": {
            "address": "172.21.0.7:44374"
        }
    },
    "related": {
        "ip": [
            "81.2.69.143",
            "81.2.69.145"
        ],
        "user": [
            "81.2.69.145"
        ]
    },
    "source": {
        "ip": "81.2.69.145",
        "port": 0
    },
    "tags": [
        "forwarded",
        "zscaler_zia-tunnel"
    ],
    "user": {
        "name": "81.2.69.145"
    },
    "zscaler_zia": {
        "tunnel": {
            "action": {
                "type": "IPSec Phase2"
            },
            "authentication": {
                "algorithm": "HMAC-SHA-1",
                "type": "None"
            },
            "destination": {
                "end": {
                    "ip": "81.2.69.143"
                },
                "start": {
                    "ip": "81.2.69.143",
                    "port": 0
                }
            },
            "encryption": {
                "algorithm": "AES"
            },
            "ike": {
                "version": 1
            },
            "life": {
                "bytes": 0,
                "time": 3600
            },
            "location": {
                "name": "some-location"
            },
            "policy": {
                "direction": "Inbound SA Policy",
                "protocol": "Any"
            },
            "protocol": "ESP",
            "source": {
                "end": {
                    "ip": "81.2.69.145"
                },
                "start": {
                    "ip": "81.2.69.145",
                    "port": 0
                }
            },
            "spi": "123456789",
            "type": "IPSEC IKEV 1"
        }
    }
}
```

## Web Logs

**Exported fields**

| Field | Description | Type |
|---|---|---|
| @timestamp | Event timestamp. | date |
| client.ip | IP address of the client (IPv4 or IPv6). | ip |
| client.nat.ip | Translated IP of source based NAT sessions (e.g. internal client to internet). Typically connections traversing load balancers, firewalls, or routers. | ip |
| client.user.name | Short name or login of the user. | keyword |
| cloud.account.id | The cloud account or organization id used to identify different entities in a multi-tenant environment. Examples: AWS account id, Google Cloud ORG Id, or other unique identifier. | keyword |
| cloud.availability_zone | Availability zone in which this host is running. | keyword |
| cloud.image.id | Image ID for the cloud instance. | keyword |
| cloud.instance.id | Instance ID of the host machine. | keyword |
| cloud.instance.name | Instance name of the host machine. | keyword |
| cloud.machine.type | Machine type of the host machine. | keyword |
| cloud.project.id | Name of the project in Google Cloud. | keyword |
| cloud.provider | Name of the cloud provider. Example values are aws, azure, gcp, or digitalocean. | keyword |
| cloud.region | Region in which this host is running. | keyword |
| container.id | Unique container id. | keyword |
| container.image.name | Name of the image the container was built on. | keyword |
| container.labels | Image labels. | object |
| container.name | Container name. | keyword |
| data_stream.dataset | Data stream dataset. | constant_keyword |
| data_stream.namespace | Data stream namespace. | constant_keyword |
| data_stream.type | Data stream type. | constant_keyword |
| ecs.version | ECS version this event conforms to. `ecs.version` is a required field and must exist in all events. When querying across multiple indices -- which may conform to slightly different ECS versions -- this field lets integrations adjust to the schema version of the events. | keyword |
| event.action | The action captured by the event. This describes the information in the event. It is more specific than `event.category`. Examples are `group-add`, `process-started`, `file-created`. The value is normally defined by the implementer. | keyword |
| event.dataset | Event dataset | constant_keyword |
| event.module | Event module | constant_keyword |
| event.risk_score | Risk score or priority of the event (e.g. security solutions). Use your system's original value here. | float |
| host.architecture | Operating system architecture. | keyword |
| host.containerized | If the host is a container. | boolean |
| host.domain | Name of the domain of which the host is a member. For example, on Windows this could be the host's Active Directory domain or NetBIOS domain name. For Linux this could be the domain of the host's LDAP provider. | keyword |
| host.hostname | Hostname of the host. It normally contains what the `hostname` command returns on the host machine. | keyword |
| host.id | Unique host id. As hostname is not always unique, use values that are meaningful in your environment. Example: The current usage of `beat.name`. | keyword |
| host.ip | Host ip addresses. | ip |
| host.mac | Host mac addresses. | keyword |
| host.name | Name of the host. It can contain what `hostname` returns on Unix systems, the fully qualified domain name, or a name specified by the user. The sender decides which value to use. | keyword |
| host.os.build | OS build information. | keyword |
| host.os.codename | OS codename, if any. | keyword |
| host.os.family | OS family (such as redhat, debian, freebsd, windows). | keyword |
| host.os.kernel | Operating system kernel version as a raw string. | keyword |
| host.os.name | Operating system name, without the version. | keyword |
| host.os.platform | Operating system platform (such centos, ubuntu, windows). | keyword |
| host.os.version | Operating system version as a raw string. | keyword |
| host.type | Type of host. For Cloud providers this can be the machine type like `t2.medium`. If vm, this could be the container, for example, or other information meaningful in your environment. | keyword |
| http.request.bytes | Total size in bytes of the request (body and headers). | long |
| http.request.method | HTTP request method. The value should retain its casing from the original event. For example, `GET`, `get`, and `GeT` are all considered valid values for this field. | keyword |
| http.request.mime_type | Mime type of the body of the request. This value must only be populated based on the content of the request body, not on the `Content-Type` header. Comparing the mime type of a request with the request's Content-Type header can be helpful in detecting threats or misconfigured clients. | keyword |
| http.request.referrer | Referrer for this HTTP request. | keyword |
| http.response.bytes | Total size in bytes of the response (body and headers). | long |
| http.response.status_code | HTTP response status code. | long |
| input.type | Input type | keyword |
| log.offset | Log offset | long |
| log.source.address | Source address from which the log event was read / sent from. | keyword |
| network.protocol | In the OSI Model this would be the Application Layer protocol. For example, `http`, `dns`, or `ssh`. The field value must be normalized to lowercase for querying. | keyword |
| related.hosts | All hostnames or other host identifiers seen on your event. Example identifiers include FQDNs, domain names, workstation names, or aliases. | keyword |
| related.ip | All of the IPs seen on your event. | ip |
| rule.name | The name of the rule or signature generating the event. | keyword |
| rule.ruleset | Name of the ruleset, policy, group, or parent category in which the rule used to generate this event is a member. | keyword |
| tags | List of keywords used to tag each event. | keyword |
| url.domain | Domain of the url, such as "www.elastic.co". In some cases a URL may refer to an IP and/or port directly, without a domain name. In this case, the IP address would go to the `domain` field. If the URL contains a literal IPv6 address enclosed by `[` and `]` (IETF RFC 2732), the `[` and `]` characters should also be captured in the `domain` field. | keyword |
| url.extension | The field contains the file extension from the original request url, excluding the leading dot. The file extension is only set if it exists, as not every url has a file extension. The leading period must not be included. For example, the value must be "png", not ".png". Note that when the file name has multiple extensions (example.tar.gz), only the last one should be captured ("gz", not "tar.gz"). | keyword |
| url.fragment | Portion of the url after the `#`, such as "top". The `#` is not part of the fragment. | keyword |
| url.original | Unmodified original url as seen in the event source. Note that in network monitoring, the observed URL may be a full URL, whereas in access logs, the URL is often just represented as a path. This field is meant to represent the URL as it was observed, complete or not. | wildcard |
| url.password | Password of the request. | keyword |
| url.path | Path of the request, such as "/search". | wildcard |
| url.port | Port of the request, such as 443. | long |
| url.query | The query field describes the query string of the request, such as "q=elasticsearch". The `?` is excluded from the query string. If a URL contains no `?`, there is no query field. If there is a `?` but no query, the query field exists with an empty string. The `exists` query can be used to differentiate between the two cases. | keyword |
| url.scheme | Scheme of the request, such as "https". Note: The `:` is not part of the scheme. | keyword |
| url.username | Username of the request. | keyword |
| user.email | User email address. | keyword |
| user_agent.device.name | Name of the device. | keyword |
| user_agent.name | Name of the user agent. | keyword |
| user_agent.original | Unparsed user_agent string. | keyword |
| user_agent.os.full | Operating system name, including the version or code name. | keyword |
| user_agent.os.name | Operating system name, without the version. | keyword |
| user_agent.os.version | Operating system version as a raw string. | keyword |
| user_agent.version | Version of the user agent. | keyword |
| zscaler_zia.web.app.class | The web application class of the application that was accessed. Equivalent to module. | keyword |
| zscaler_zia.web.app.name | Cloud application name. | keyword |
| zscaler_zia.web.bandwidth_throttle | Indicates whether the transaction was throttled due to a configured bandwidth policy. | keyword |
| zscaler_zia.web.client.internet.ip | The client Internet (NATted Public) IP address. This is different from the cip value if the internal IP address is visible. Otherwise, same as cip. | keyword |
| zscaler_zia.web.ctime | The time from when the first byte of the request hits the ZEN to the time in which the last byte of the response is sent from the ZEN back to the browser. | long |
| zscaler_zia.web.department | Department of the user. | keyword |
| zscaler_zia.web.device.hostname | The obfuscated version of the device owner. This field must be changed manually. | keyword |
| zscaler_zia.web.dpl.dictionaries | The DLP dictionaries that were matched, if any. | keyword |
| zscaler_zia.web.dpl.engine | The DLP engine that was matched, if any. | keyword |
| zscaler_zia.web.encoded_host | Encoded version of the destination host name. | keyword |
| zscaler_zia.web.file.class | Type of file associated with the transaction. | keyword |
| zscaler_zia.web.file.type | Type of file associated with the transaction. | keyword |
| zscaler_zia.web.location | Gateway location or sub-location of the source. | keyword |
| zscaler_zia.web.malware.category | The category of malware that was detected in the transaction, if any. Also indicates if a file was submitted to the Sandbox engine for analysis and the result of the analysis. | keyword |
| zscaler_zia.web.malware.class | The class of malware that was detected in the transaction, if any. | keyword |
| zscaler_zia.web.record.id | N/A | keyword |
| zscaler_zia.web.stime | The round trip time between the ZEN request and the server. | long |
| zscaler_zia.web.threat.name | The name of the threat that was detected in the transaction, if any. | keyword |
| zscaler_zia.web.total.size | Total size, in bytes, of the HTTP transaction; sum of the total request size and total response size. | long |
| zscaler_zia.web.unscannable.type | Unscannable file type. | keyword |
| zscaler_zia.web.url.category.sub | Category of the destination URL. | keyword |
| zscaler_zia.web.url.category.super | Super category of the destination URL. | keyword |
| zscaler_zia.web.url.class | Class of the destination URL. | keyword |


An example event for `web` looks as following:

```json
{
    "@timestamp": "2021-12-17T07:04:57.000Z",
    "agent": {
        "ephemeral_id": "ced1fd2e-2f17-4f67-b8b1-d38a1920abbb",
        "hostname": "docker-fleet-agent",
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "name": "docker-fleet-agent",
        "type": "filebeat",
        "version": "7.16.2"
    },
    "client": {
        "ip": "81.2.69.193",
        "nat": {
            "ip": "81.2.69.145"
        },
        "user": {
            "name": "administrator1"
        }
    },
    "data_stream": {
        "dataset": "zscaler_zia.web",
        "namespace": "ep",
        "type": "logs"
    },
    "ecs": {
        "version": "8.0.0"
    },
    "elastic_agent": {
        "id": "d03794ae-c5b7-46b2-8a63-42f00010ac23",
        "snapshot": false,
        "version": "7.16.2"
    },
    "event": {
        "action": "blocked",
        "agent_id_status": "verified",
        "category": "web",
        "dataset": "zscaler_zia.web",
        "ingested": "2022-02-04T08:48:57Z",
        "kind": "event",
        "risk_score": 0,
        "type": [
            "info"
        ]
    },
    "http": {
        "request": {
            "bytes": 600,
            "method": "CONNECT",
            "mime_type": "Other",
            "referrer": "None"
        },
        "response": {
            "bytes": 65,
            "status_code": 200
        }
    },
    "input": {
        "type": "tcp"
    },
    "log": {
        "source": {
            "address": "172.21.0.7:48722"
        }
    },
    "network": {
        "protocol": "http_proxy"
    },
    "related": {
        "hosts": [
            "TestMachine35"
        ],
        "ip": [
            "81.2.69.193",
            "81.2.69.145"
        ]
    },
    "rule": {
        "name": "Zscaler Proxy Traffic",
        "ruleset": "FwFilter"
    },
    "tags": [
        "forwarded",
        "zscaler_zia-web"
    ],
    "url": {
        "extension": "com",
        "original": "www.example.com",
        "path": "www.example.com"
    },
    "user": {
        "email": "test@example.com"
    },
    "user_agent": {
        "device": {
            "name": "Other"
        },
        "name": "Other",
        "original": "Windows Microsoft Windows 10 Pro ZTunnel/1.0",
        "os": {
            "full": "Windows 10",
            "name": "Windows",
            "version": "10"
        }
    },
    "zscaler_zia": {
        "web": {
            "app": {
                "class": "General Browsing",
                "name": "General Browsing"
            },
            "ctime": 0,
            "department": "Unknown",
            "device": {
                "hostname": "TestMachine35"
            },
            "dpl": {
                "dictionaries": "None",
                "engine": "None"
            },
            "location": "Test DB",
            "malware": {
                "category": "None"
            },
            "stime": 0,
            "threat": {
                "name": "None"
            },
            "unscannable": {
                "type": "None"
            },
            "url": {
                "category": {
                    "sub": "Web Search",
                    "super": "Information Technology"
                },
                "class": "Business Use"
            }
        }
    }
}
```
