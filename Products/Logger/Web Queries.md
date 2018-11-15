## Content
- [Health](#-health)
    - [Device Health Check](#-device-health-check)
    - [Forwarder EPS](#-forwarder-eps)
    - [Receiver EPS](#-receiver-eps)
    - [Total Logger EPS](#-total-logger-eps)
    - [Caching Connectors](#-caching-connectors)
    - [Uncategorized Events](#-uncategorized-events)
- [Agents](#-agents)
    - [Top Agent Version Count with Agent Name](#-top-agent-version-count-with-agent-name)
    - [Top agentTimeZone](#-top-agenttimezone)
    - [Bytes to GB evaluation](#-bytes-to-gb-evaluation)
    - [Lookup against a list](#-lookup-against-a-list)
- [Microsoft](#-microsoft)
    - [Kerboroasting](#-kerboroasting)
    
## [↑](#content) Health
#### [↑](#content) Device Health Check
```sql
((deviceVendor = "ArcSight" AND deviceEventClassId = "hardware:122" ) OR ( deviceEventClassId = "hardware:102" ) OR ( deviceEventClassId = "hardware:103" ) OR ( deviceEventClassId = "hardware:132" ) OR ( deviceEventClassId = "hardware:133" ) OR ( deviceEventClassId = "hardware:142" ) OR ( deviceEventClassId = "hardware:143" ) OR ( deviceEventClassId = "raid:102" ) OR ( deviceEventClassId = "raid:103" ) OR ( deviceEventClassId = "raid:112" ) OR ( deviceEventClassId = "raid:113" ) OR ( deviceEventClassId = "raid:122" ) OR ( deviceEventClassId = "raid:123" ) OR ( deviceEventClassId = "hardware:152" ) OR ( deviceEventClassId = "hardware:153" ) OR ( deviceEventClassId = "hardware:112" ) OR ( deviceEventClassId = "hardware:113" ) OR ( deviceEventClassId = "connectorappliance:202" ) OR ( deviceEventClassId = "connectorappliance:202" ) OR ( deviceEventClassId = "connectorappliance:205" ) OR ( deviceEventClassId = "connectorappliance:206" ) OR ( deviceEventClassId = "connectorappliance:106" ) OR ( deviceEventClassId = "connectorappliance:107" ) OR ( deviceEventClassId = "connectorappliance:207" ) OR ( deviceEventClassId = "connectorappliance:108" ) OR ( deviceEventClassId = "connectorappliance:109" ) OR ( deviceEventClassId = "destination:202" ) OR ( deviceEventClassId = "destination:203" ) OR ( deviceEventClassId = "destination:103" ) OR ( deviceEventClassId = "destination:204" ) OR ( deviceEventClassId = "destination:205" ) OR ( deviceEventClassId = "destination:206" ) OR ( deviceEventClassId = "destination:207" ) OR ( deviceEventClassId = "container:201" ) OR ( deviceEventClassId = "container:202" ) OR ( deviceEventClassId = "container:204" ) OR ( deviceEventClassId = "container:205" ) OR ( deviceEventClassId = "container:206" ) OR ( deviceEventClassId = "container:209" ) OR ( deviceEventClassId = "container:210" ) OR ( deviceEventClassId = "container:212" ) OR ( deviceEventClassId = "container:114" ) OR ( deviceEventClassId = "location:201" ) OR ( deviceEventClassId = "container:103" ) OR ( deviceEventClassId = "host:201" ) OR ( deviceEventClassId = "host:304" ) OR ( deviceEventClassId = "platform:200" ) OR ( deviceEventClassId = "platform:203" ) OR ( deviceEventClassId = "platform:228" ) OR ( deviceEventClassId = "platform:232" ) OR ( deviceEventClassId = "platform:233" ) OR ( deviceEventClassId = "platform:234" ) OR ( deviceEventClassId = "platform:235" ) OR ( deviceEventClassId = "platform:236" ) OR ( deviceEventClassId = "platform:237" ) OR ( deviceEventClassId = "platform:238" ) OR ( deviceEventClassId = "platform:244" ) OR ( deviceEventClassId = "platform:247" ) OR ( deviceEventClassId = "platform:252" ) OR ( deviceEventClassId = "platform:253" ) OR ( deviceEventClassId = "platform:270" ) OR ( deviceEventClassId = "platform:280" ) OR ( deviceEventClassId = "platform:281" ) OR ( deviceEventClassId = "platform:282" ) OR (deviceEventClassId = "platform:307" ) OR ( deviceEventClassId = "platform:308" ) ) | top deviceEventClassId deviceAddress
```
#### [↑](#content) Forwarder EPS
```sql
deviceEventClassId = eps:103 | chart avg(deviceCustomNumber1) by deviceCustomString6
```
#### [↑](#content) Receiver EPS
```sql
deviceEventClassId = eps:102 | chart avg(deviceCustomNumber1) by deviceCustomString6
```
#### [↑](#content) Total Logger EPS
```sql
deviceEventClassId = eps:100 | chart avg(deviceCustomNumber1) by deviceAddress
```
#### [↑](#content) Caching Connectors
```sql
deviceEventClassId = "agent:019" | top agentType
```
#### [↑](#content) Uncategorized Events
```sql
categoryDeviceGroup IS NULL AND categoryTechnique IS NULL AND categorySignificance IS NULL AND categoryObject IS NULL AND categoryBehavior IS NULL AND categoryOutcome IS NULL | chart count by name deviceProduct
```
## [↑](#content) Agents
#### [↑](#content) Top Agent Version Count with Agent Name
```sql
deviceProduct=ArcSight | rex "av=(?<av>[^\s]+)\s" | top av agentHostName | sort agentHostName
```
#### [↑](#content) Top agentTimeZone 
```sql
deviceProduct=ArcSight |  rex "atz=(?<agentTimeZone>[^\s]+)\s" | where agentTimeZone != "UTC" | top sourceHostName
```
#### [↑](#content) Bytes to GB evaluation
```sql
sourceAddress IS NOT NULL AND bytesIn >0 | chart sum(bytesIn) as BI by sourceAddress | eval kbi=BI/1000 | eval mbi=BI/1000000
```
#### [↑](#content) Lookup against a list
```sql
deviceVendor="Palo Alto Networks" and name=TRAFFIC | lookup + LISTNAMEHERE IP_Address as destinationAddress
```
## [↑](#content) Microsoft
#### [↑](#content) Kerboroasting
```sql
deviceProduct = "Microsoft Windows" AND deviceEventClassId = "Microsoft-Windows-Security-Auditing:4769" AND deviceSeverity != Audit_success AND NOT destinationServiceName CONTAINS "$" | rex "ad\.TicketEncryptionType=(?<TicketEncryptionType>[^\s] )\s" | rex "ad\.TicketOptions=(?<TicketOptions>[^\s] )\s" | WHERE TicketOptions = 0x40810000 AND TicketEncryptionType = 0x17 | top destinationUserName, destinationServiceName
```
