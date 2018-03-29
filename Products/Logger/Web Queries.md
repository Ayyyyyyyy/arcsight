# Detail
This section will contains queries used in the web gui for direct lookup.

#### Top Agent Version Count
```sql
deviceProduct=ArcSight | rex "av=(?<av>[^\s]+)\s" | top av
```
#### Top Agent Version Count with Agent Name
```sql
deviceProduct=ArcSight | rex "av=(?<av>[^\s]+)\s" | top av agentHostName | sort agentHostName
```
#### Top agentTimeZone 
```sql
deviceProduct=ArcSight |  rex "atz=(?<agentTimeZone>[^\s]+)\s" | where agentTimeZone != "UTC" | top sourceHostName
```
#### Bytes to GB evaluation
```sql
sourceAddress IS NOT NULL AND bytesIn >0 | chart sum(bytesIn) as BI by sourceAddress | eval kbi=BI/1000 | eval mbi=BI/1000000
```
#### Lookup against a list
```sql
deviceVendor="Palo Alto Networks" and name=TRAFFIC | lookup + LISTNAMEHERE IP_Address as destinationAddress
```
