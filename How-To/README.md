# Documentation and How To's
This section will contain documentation and how to articles that pertain to arcsight.
# Logger Queries(placeholder for now - still need to create it's own page.)
## General
#### Data Use (Daily)
```sql
SELECT
    DATE(events.arc_deviceReceiptTime) as "Day",
    SUM(events.arc_deviceCustomString4) as "Total (Bytes)",
    SUM(events.arc_deviceCustomString4)/1024 as "Total (Kilobytes)",
    SUM(events.arc_deviceCustomString4)/1024/1024 as "Total (Megabytes)",
    SUM(events.arc_deviceCustomString4)/1024/1024/1024 as "Total (Gigabytes)"
FROM events
WHERE
    events.arc_deviceVendor = "ArcSight"
AND
    events.arc_deviceEventClassId = "agent:050"
GROUP BY
    DATE(events.arc_deviceReceiptTime)
ORDER
    BY DATE(events.arc_deviceReceiptTime) ASC
```
#### GB Per Hour
```sql
SELECT
DATE(events.arc_deviceReceiptTime) as "Day",
HOUR(events.arc_deviceReceiptTime) as "Hour",
sum(events.arc_deviceCustomString4) as "Total (Bytes)",
sum(events.arc_deviceCustomString4)/1024 as "Total (Kilobytes)", sum(events.arc_deviceCustomString4)/1024/1024 as "Total (Megabytes)", sum(events.arc_deviceCustomString4)/1024/1024/1024 as "Total (Gigabytes)"
FROM events
WHERE
events.arc_deviceEventClassId="agent:050"
GROUP BY
DATE(events.arc_deviceReceiptTime),HOUR(events.arc_deviceReceiptTime)
ORDER BY
DATE(events.arc_deviceReceiptTime),HOUR(events.arc_deviceReceiptTime)
```
#### Raw Event Size Average
```sql
SELECT
    SUM(events.arc_deviceCustomNumber3) as "Total_event",
    SUM(events.arc_deviceCustomString4) as "Total_raw_size_bytes",
    SUM((events.arc_deviceCustomString4)/1048576) as "Total_raw_size_MB",
    (SUM(events.arc_deviceCustomString4)/SUM(events.arc_deviceCustomNumber3)) AS "avg_event_size_bytes"
FROM events
WHERE
    events.arc_deviceEventClassId = 'agent:050'
```
#### Device/Vendor Product Counts (Monthy)
```sql
SELECT
    UPPER(events.arc_deviceVendor) as "Vendor",
    LOWER(events.arc_deviceProduct) as "Product",
    CONCAT(MONTHNAME(events.arc_deviceReceiptTime), " ", YEAR(events.arc_deviceReceiptTime)) as "Month",
    COUNT(events.arc_baseEventCount) AS "Events"
FROM events
WHERE    
    events.arc_deviceVendor IS NOT NULL
GROUP BY
    events.arc_deviceVendor,
    events.arc_deviceProduct,
    CONCAT(MONTHNAME(events.arc_deviceReceiptTime), " ",
    YEAR(events.arc_deviceReceiptTime))
ORDER BY
    events.arc_deviceVendor,
    events.arc_deviceProduct,
    CONCAT(MONTHNAME(events.arc_deviceReceiptTime), " ",
    YEAR(events.arc_deviceReceiptTime))
```
#### Device/Vendor Product Counts v2 (Monthly)
```sql
SELECT
    UPPER(events.arc_deviceVendor) as "Vendor",
    LOWER(events.arc_deviceProduct) as "Product",
    UPPER(events.arc_customerName) "Customer",
    CONCAT(MONTHNAME(events.arc_deviceReceiptTime), " ",
    YEAR(events.arc_deviceReceiptTime)) as "Month",
    count(events.arc_baseEventCount) as "Events"
  
FROM events
WHERE
    events.arc_deviceVendor != "ArcSight"
GROUP BY
    events.arc_deviceVendor,
    events.arc_deviceProduct,
CONCAT(MONTHNAME(events.arc_deviceReceiptTime), " ",
    YEAR(events.arc_deviceReceiptTime))
ORDER BY
    events.arc_deviceVendor,
    events.arc_deviceProduct,
CONCAT(MONTHNAME(events.arc_deviceReceiptTime), " ",
    YEAR(events.arc_deviceReceiptTime))
```
#### Event Counts (Daily)
```sql
SELECT
    UPPER(events.arc_deviceVendor) "Vendor",
    LOWER(events.arc_deviceProduct) "Product",
    UPPER(events.arc_customerName) "Customer",
    SUM(events.arc_baseEventCount) "Count"
FROM events
WHERE
     events.arc_deviceVendor IS NOT NULL
GROUP BY
    events.arc_deviceVendor ASC
ORDER BY
    events.arc_deviceVendor ASC,
    SUM(events.arc_baseEventCount) DESC,
    events.arc_deviceProduct ASC
```
#### Denied Connections by Address
```sql
SELECT
    events.arc_sourceZoneURI "Zone",
    events.arc_sourceAddress "Address",
    events.arc_sourceHostName "Host Name",
    SUM(events.arc_baseEventCount) "Count"
FROM events
WHERE
    events.arc_categoryBehavior = '/Access'
    AND events.arc_categoryDeviceGroup = '/Firewall'
    AND events.arc_categoryObject = '/Host/Application/Service'
    AND events.arc_categoryOutcome = '/Failure'
GROUP BY
    events.arc_sourceZoneURI,
    events.arc_sourceAddress,
    events.arc_sourceHostName,
    events.arc_sourceAddress
ORDER BY
    events.arc_sourceZoneURI,
    events.arc_sourceAddress,
    events.arc_sourceHostName
```
#### Common Account Login Failures
```sql
SELECT 
    events.arc_categoryObject "Resource Type",
    events.arc_sourceNtDomain "Source NT Domain",
    events.arc_sourceHostName "Source Host Name",
    events.arc_sourceZoneURI "Source Zone",
    events.arc_sourceAddress "Source Address",
    events.arc_sourceUserId "Source User ID",
    events.arc_sourceUserName "Source User Name",
    SUM(events.arc_baseEventCount) "Count"
FROM    events
WHERE
    (
        events.arc_categoryBehavior = '/Access/Start'
          OR events.arc_categoryBehavior = '/Authentication/Verify'
          OR events.arc_categoryBehavior = '/Authorization/Verify'
    )
AND events.arc_categoryOutcome = '/Failure'
GROUP BY
    events.arc_categoryObject,
    events.arc_sourceZoneURI,
    events.arc_sourceAddress,
    events.arc_sourceHostName,
    events.arc_sourceNtDomain,
    events.arc_sourceUserId,
    events.arc_sourceUserName
ORDER BY
    SUM(events.arc_baseEventCount) DESC,
    events.arc_categoryObject,
    events.arc_sourceZoneURI,
    events.arc_sourceAddress,
    events.arc_sourceHostName,
    events.arc_sourceNtDomain,
    events.arc_sourceUserId,
    events.arc_sourceUserName
```
#### Top Machines Accessing the Web
```sql
SELECT  events.arc_sourceZoneURI "Source Zone",
        events.arc_sourceAddress "Source Address",
        events.arc_sourceHostName "Source Host Name",
        events.arc_destinationPort "Destination Port",
        SUM(events.arc_baseEventCount) "Count"
FROM    events
WHERE events.arc_destinationPort IN (<%webPorts%>)
    AND events.arc_bytesOut        > 0
GROUP BY events.arc_sourceZoneURI,
        events.arc_sourceAddress ,
        events.arc_sourceHostName,
        events.arc_destinationPort
ORDER BY SUM(events.arc_baseEventCount) DESC,
        events.arc_sourceZoneURI,
        events.arc_sourceAddress ,
        events.arc_sourceHostName,
        events.arc_destinationPort
```
#### Web Proxy User Data (PALO)
```sql
SELECT
        DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H:%i") "EndTime",
        events.arc_sourceHostName "Source HostName",
        events.arc_sourceAddress "Source Address",
        UPPER(events.arc_sourceUserName) "Source User",
        events.arc_destinationHostName "Destination Host Name",
        events.arc_requestUrl "Destination URL",
        events.arc_destinationAddress "Destination Address",
        events.arc_applicationProtocol "Protocol",
        events.arc_deviceCustomString2 "Proxy Category",
        events.arc_requestClientApplication "User Agent",
FROM     events
WHERE
        events.arc_deviceVendor = "Palo Alto Networks"
AND
    (
        events.arc_sourceUserName IS NOT NULL
    OR  events.arc_destinationHostName IS NOT NULL
    OR    events.arc_sourceUserName != "-"
    )
GROUP BY
        UPPER(events.arc_sourceUserName)
ORDER BY
        DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H:%i") DESC,
        UPPER(events.arc_sourceUserName) DESC,
        events.arc_destinationHostName DESC
```
## Agents
#### Agents Sending Events
```sql
SELECT
    UPPER(events.arc_agentHostName) "Agent Host Name",
    UPPER(events.arc_agentType) "Agent Type",
    UPPER(events.arc_deviceVendor) "Vendor",
    UPPER(events.arc_deviceProduct) "Product",
    SUM(events.arc_baseEventCount) "Count"
FROM  events
WHERE
    events.arc_agentHostName IS NOT NULL
GROUP BY
    events.arc_deviceVendor ASC
ORDER BY
    SUM(events.arc_baseEventCount) DESC,
    events.arc_agentHostName ASC
```
## Vendors
#### Palo Alto Networks
```sql
SELECT
    UPPER(events.arc_deviceVendor) "Vendor",
    UPPER(events.arc_deviceProduct) "Product",
    UPPER(events.arc_deviceHostName) "Device Host Name",
    LOWER(events.arc_name) "Name",
    LOWER(events.arc_deviceEventClassId) "PAN Event Type",
    SUM(events.arc_baseEventCount) "Count"
FROM  events
WHERE
    events.arc_deviceVendor = "Palo Alto Networks"
GROUP BY
    events.arc_deviceHostName ASC
ORDER BY
    SUM(events.arc_baseEventCount) DESC,
    events.arc_name ASC,
    events.arc_deviceProduct ASC
```
#### Cisco
```sql
SELECT
    UPPER(events.arc_deviceVendor) "Vendor",
    UPPER(events.arc_deviceProduct) "Product",
    UPPER(events.arc_deviceHostName) "Device Host Name",
    LOWER(events.arc_name) "Name",
    LOWER(events.arc_deviceEventClassId) "Event Type",
    SUM(events.arc_baseEventCount) "Count"
FROM  events
WHERE
    events.arc_deviceVendor = "CISCO"
GROUP BY
    events.arc_deviceHostName ASC
ORDER BY
    SUM(events.arc_baseEventCount) DESC,
    events.arc_name ASC,
    events.arc_deviceProduct ASC
```
#### Check Point
```sql
SELECT
    UPPER(events.arc_deviceVendor) "Vendor",
    UPPER(events.arc_deviceProduct) "Product",
    UPPER(events.arc_deviceHostName) "Device Host Name",
    UPPER(events.arc_deviceEventClassId) "Event ID",
    LOWER(events.arc_name) "Name",
    SUM(events.arc_baseEventCount) "Count"
FROM events
WHERE
    events.arc_deviceVendor LIKE "Check"
GROUP BY
    events.arc_deviceHostName ASC
ORDER BY
    SUM(events.arc_baseEventCount) DESC,
    events.arc_name ASC,
    events.arc_deviceProduct ASC
```
#### Damballa
```sql
SELECT
    UPPER(events.arc_deviceVendor) "Vendor",
    UPPER(events.arc_deviceProduct) "Product",
    UPPER(events.arc_deviceHostName) "Device Host Name",
    UPPER(events.arc_deviceEventClassId) "Event ID",
    LOWER(events.arc_name) "Name",
    SUM(events.arc_baseEventCount) "Count"
FROM events
WHERE
    events.arc_deviceVendor = "Damballa"
GROUP BY
    events.arc_deviceHostName ASC
ORDER BY
    SUM(events.arc_baseEventCount) DESC,
    events.arc_name ASC,
    events.arc_deviceProduct ASC
```
#### Windows
```sql
SELECT
    UPPER(events.arc_deviceVendor) "Vendor",
    UPPER(events.arc_deviceProduct) "Product",
    UPPER(events.arc_deviceHostName) "Device Host Name",
    UPPER(events.arc_deviceEventClassId) "Event ID",
    LOWER(events.arc_name) "Name",
    SUM(events.arc_baseEventCount) "Count"
FROM events
WHERE
    events.arc_deviceVendor = "Microsoft"
GROUP BY
    events.arc_deviceHostName ASC
ORDER BY
    SUM(events.arc_baseEventCount) DESC,
    events.arc_name ASC,
    events.arc_deviceProduct ASC
```
## Network
#### Device Critical Events
```sql
SELECT events.arc_deviceZoneURI "Device Zone",
      events.arc_deviceAddress "Device Address",
      events.arc_deviceHostName "Device Host Name",
      events.arc_deviceVendor "Vendor",
      events.arc_deviceProduct "Product",
      events.arc_name "Event",
      events.arc_endTime "End Time"
FROM  events
WHERE events.arc_agentSeverity LIKE '%High'
  AND
      (
            events.arc_type != 2
         OR events.arc_type IS NULL
      )
  AND
      (
            events.arc_categoryDeviceGroup LIKE '/Network Equipment%'
         OR
            (
                  events.arc_categoryDeviceGroup = '/Operating System'
              AND events.arc_categoryObject LIKE '/Network%'
            )
      )
ORDER BY events.arc_deviceZoneURI,
      events.arc_deviceAddress,
      events.arc_deviceHostName,
      events.arc_deviceVendor,
      events.arc_deviceProduct,
      events.arc_name,
      events.arc_endTime
```
#### Device Errors
```sql
SELECT events.arc_deviceZoneURI "Device Zone",
      events.arc_deviceAddress "Device Address",
      events.arc_deviceHostName "Device Host Name",
      events.arc_deviceVendor "Vendor",
      events.arc_deviceProduct "Product",
      events.arc_name "Error",
      events.arc_endTime "End Time"
FROM  events
WHERE events.arc_categorySignificance = '/Informational/Error'
  AND
      (
            events.arc_type != 2
         OR events.arc_type IS NULL
      )
  AND
      (
            events.arc_categoryDeviceGroup LIKE '/Network Equipment%'
         OR
            (
                  events.arc_categoryDeviceGroup = '/Operating System'
              AND events.arc_categoryObject LIKE '/Network%'
            )
      )
ORDER BY events.arc_deviceZoneURI,
      events.arc_deviceAddress,
      events.arc_deviceHostName,
      events.arc_deviceVendor,
      events.arc_deviceProduct,
      events.arc_name,
      events.arc_endTime
```
#### Device Events
```sql
SELECT events.arc_deviceZoneURI "Device Zone",
      events.arc_deviceAddress "Device Address",
      events.arc_deviceHostName "Device Host Name",
      events.arc_deviceVendor "Vendor",
      events.arc_deviceProduct "Product",
      events.arc_name "Event",
      SUM( events.arc_baseEventCount ) "Count"
FROM  events
WHERE
      (
            events.arc_type != 2
         OR events.arc_type IS NULL
      )
  AND
      (
            events.arc_categoryDeviceGroup LIKE '/Network Equipment%'
         OR
            (
                  events.arc_categoryDeviceGroup = '/Operating System'
              AND events.arc_categoryObject LIKE '/Network%'
            )
      )
GROUP BY events.arc_deviceZoneURI,
      events.arc_deviceAddress,
      events.arc_deviceHostName,
      events.arc_deviceVendor,
      events.arc_deviceProduct,
      events.arc_name
ORDER BY events.arc_deviceZoneURI,
      events.arc_deviceAddress,
      events.arc_deviceHostName,
      SUM( events.arc_baseEventCount ) DESC
```
#### Device Interface Down
```sql
SELECT events.arc_deviceHostName "Device Host Name",
    events.arc_deviceVendor  "Vendor",
    events.arc_deviceProduct "Product",
    events.arc_deviceZoneURI "Device Zone",
    events.arc_deviceAddress "Device Address",
    events.arc_deviceInboundInterface "Inbound Interface",
    events.arc_deviceOutboundInterface "Outbound Interface",
    events.arc_name "Event",
    events.arc_endTime "Time"
FROM events
WHERE events.arc_categoryDeviceGroup LIKE '/Network Equipment%'
AND events.arc_categoryObject LIKE '/Host/Resource/Interface%'
AND LOWER( events.arc_deviceProduct ) NOT LIKE LOWER( '%VPN%' )
AND LOWER( events.arc_categoryBehavior ) LIKE LOWER( '%stop%' )
AND
(
        LOWER( events.arc_name) LIKE LOWER( '%link%' )
     OR LOWER( events.arc_name) LIKE LOWER( '%down%' )
)
AND
(
        events.arc_deviceInboundInterface IS NOT NULL
     OR events.arc_deviceOutboundInterface IS NOT NULL
)
ORDER BY events.arc_endTime
```
#### Interface Status Messages
```sql
SELECT events.arc_deviceZoneURI "Device Zone",
      events.arc_deviceAddress "Device Address",
      events.arc_deviceHostName "Device Host Name",
      events.arc_name "Status Message",
      events.arc_deviceInboundInterface "Inbound Interface",
      events.arc_deviceOutboundInterface "Outbound Interface",
      events.arc_deviceVendor "Vendor",
      events.arc_deviceProduct "Product",
      SUM(events.arc_baseEventCount) "Count"
FROM  events
WHERE events.arc_categoryDeviceGroup LIKE '/Network Equipment%'
  AND events.arc_categoryObject LIKE '/Host/Resource/Interface%'
  AND UPPER(events.arc_deviceProduct) NOT LIKE '%VPN%'
  AND
      (
            UPPER(events.arc_name) LIKE UPPER('%link%')
         OR UPPER(events.arc_name) LIKE UPPER('%port%')
         OR UPPER(events.arc_name) LIKE UPPER('%interface%')
         OR
            (
                  UPPER(events.arc_name) LIKE UPPER('%up%')
              AND UPPER(events.arc_categoryBehavior) LIKE UPPER('%start%')
            )
         OR
            (
                  UPPER(events.arc_name) LIKE UPPER('%down%')
              AND UPPER(events.arc_categoryBehavior) LIKE UPPER('%stop%')
            )
      )
  AND
      (
            events.arc_type != 2
         OR events.arc_type IS NULL
      )
GROUP BY events.arc_deviceZoneURI,
      events.arc_deviceAddress,
      events.arc_deviceHostName,
      events.arc_name,
      events.arc_deviceVendor,
      events.arc_deviceProduct
ORDER BY SUM(events.arc_baseEventCount) DESC
```
#### SNMP Authentication Failures
```sql
SELECT events.arc_destinationUserId "User ID",
      events.arc_destinationUserName "User Name",
      events.arc_destinationZoneURI "Zone",
      events.arc_destinationAddress "Address",
      events.arc_destinationHostName "Host Name",
      events.arc_deviceVendor "Device Vendor",
      events.arc_deviceProduct "Device Product",
      SUM(events.arc_baseEventCount) "Count"
FROM  events
WHERE events.arc_categoryOutcome != '/Success'
  AND
      (
            UPPER(events.arc_applicationProtocol) LIKE UPPER('%snmp%')
         OR UPPER(events.arc_name) LIKE UPPER('%snmp%')
      )
  AND
      (
            events.arc_categoryBehavior = '/Authentication/Verify'
         OR events.arc_categoryBehavior = '/Authorization/Verify'
      )
  AND
      (
            events.arc_categoryObject LIKE '/Host%'
         OR events.arc_categoryObject = '/Network'
      )
GROUP BY events.arc_destinationUserId,
      events.arc_destinationUserName,
      events.arc_destinationZoneURI,
      events.arc_destinationAddress,
      events.arc_destinationHostName,
      events.arc_deviceVendor,
      events.arc_deviceProduct
ORDER BY events.arc_destinationUserId,
      events.arc_destinationUserName,
      events.arc_destinationZoneURI,
      events.arc_destinationAddress,
      events.arc_destinationHostName,
      events.arc_deviceVendor,
      events.arc_deviceProduct
```
#### Top Device Authentication Events
```sql
SELECT events.arc_destinationUserId "User ID",
      events.arc_destinationUserName "User Name",
      events.arc_destinationZoneURI "Zone",
      events.arc_destinationAddress "Address",
      events.arc_destinationHostName "Host Name",
      events.arc_deviceVendor "Device Vendor",
      events.arc_deviceProduct "Device Product",
      SUM(events.arc_baseEventCount) "Count"
FROM  events
WHERE events.arc_categoryOutcome != '/Success'
  AND
      (
            UPPER(events.arc_applicationProtocol) LIKE UPPER('%snmp%')
         OR UPPER(events.arc_name) LIKE UPPER('%snmp%')
      )
  AND
      (
            events.arc_categoryBehavior = '/Authentication/Verify'
         OR events.arc_categoryBehavior = '/Authorization/Verify'
      )
  AND
      (
            events.arc_categoryObject LIKE '/Host%'
         OR events.arc_categoryObject = '/Network'
      )
GROUP BY events.arc_destinationUserId,
      events.arc_destinationUserName,
      events.arc_destinationZoneURI,
      events.arc_destinationAddress,
      events.arc_destinationHostName,
      events.arc_deviceVendor,
      events.arc_deviceProduct
ORDER BY events.arc_destinationUserId,
      events.arc_destinationUserName,
      events.arc_destinationZoneURI,
      events.arc_destinationAddress,
      events.arc_destinationHostName,
      events.arc_deviceVendor,
      events.arc_deviceProduct
```
#### Top 10 Traffic Types
```sql
SELECT  upper(events.arc_applicationProtocol) "Application Protocol",
        events.arc_destinationPort "Port"                           ,
        upper(events.arc_transportProtocol) "Transport Protocol"    ,
        SUM(events.arc_baseEventCount) "Count"
FROM    events
WHERE (events.arc_applicationProtocol IS NOT NULL
     OR events.arc_transportProtocol  IS NOT NULL
     OR events.arc_destinationPort    IS NOT NULL)
    AND (events.arc_bytesIn             > 0
     OR events.arc_bytesOut            > 0)
GROUP BY upper(events.arc_applicationProtocol),
        events.arc_destinationPort            ,
        upper(events.arc_transportProtocol)
ORDER BY SUM(events.arc_baseEventCount) DESC ,
        upper(events.arc_applicationProtocol),
        events.arc_destinationPort           ,
        upper(events.arc_transportProtocol)
```
#### VPN Connection Attempts
```sql
SELECT  events.arc_deviceZoneURI "Device Zone",
        events.arc_deviceAddress "Device Address",
        events.arc_deviceHostName "Device Host Name",
        events.arc_name "VPN Event",
        events.arc_categoryOutcome "Result",
        events.arc_sourceUserId "Source User ID",
        events.arc_sourceHostName "Source Host Name",
        events.arc_sourceUserName "Source User Name",
        events.arc_destinationZoneURI "Destination Zone",
        events.arc_destinationAddress "Destination Address",
        events.arc_destinationHostName "Destination Host Name",
        events.arc_destinationUserId "Destination User ID",
        events.arc_destinationUserName "Destination User Name",
        SUM(events.arc_baseEventCount) "Count"
FROM    events
WHERE   events.arc_categoryDeviceGroup = '/VPN'
    AND (events.arc_categoryBehavior = '/Access/Start'
     OR events.arc_categoryBehavior = '/Authorization/Verify'
     OR events.arc_categoryBehavior = '/Authentication/Verify')
    AND events.arc_categoryOutcome != '/Failure'
GROUP BY events.arc_deviceZoneURI,
        events.arc_deviceAddress,
        events.arc_deviceHostName,
        events.arc_name,
        events.arc_categoryOutcome,
        events.arc_sourceUserId,
        events.arc_sourceHostName,
        events.arc_sourceUserName,
        events.arc_destinationZoneURI,
        events.arc_destinationAddress,
        events.arc_destinationHostName,
        events.arc_destinationUserId,
        events.arc_destinationUserName
ORDER BY events.arc_deviceZoneURI,
        events.arc_deviceAddress,
        events.arc_deviceHostName,
        events.arc_name,
        events.arc_categoryOutcome,
        events.arc_sourceUserId,
        events.arc_sourceHostName,
        events.arc_sourceUserName,
        events.arc_destinationZoneURI,
        events.arc_destinationAddress,
        events.arc_destinationHostName,
        events.arc_destinationUserId,
        events.arc_destinationUserName
```
## IDS/IPS
#### Top IDS/IPS Alerts
```sql
SELECT   events.arc_deviceEventClassId "Signature",
         events.arc_name "Signature Information",
         events.arc_deviceVendor "Vendor",
         events.arc_deviceProduct "Product",
         SUM(events.arc_baseEventCount) "Number of Events"
FROM     events
WHERE    events.arc_categoryDeviceGroup LIKE '/IDS%'
     AND events.arc_categoryDeviceGroup != '/IDS/Host/Antivirus'
     AND
         (
                  events.arc_type = 0
               OR events.arc_type IS NULL
         )
GROUP BY events.arc_deviceEventClassId,
         events.arc_name,
         events.arc_deviceVendor,
         events.arc_deviceProduct
ORDER BY SUM(events.arc_baseEventCount) DESC,
         events.arc_deviceEventClassId,
         events.arc_name
```
#### Alert Counts by Device
```sql
SELECT   events.arc_deviceVendor "Vendor",
         events.arc_deviceProduct "Product",
         events.arc_deviceZoneName "Zone",
         events.arc_deviceAddress "Address",
         events.arc_deviceHostName "Host Name",
         SUM(events.arc_baseEventCount) "Number of Events"
FROM     events
WHERE    events.arc_deviceAddress IS NOT NULL
     AND events.arc_categoryDeviceGroup LIKE '/IDS%'
     AND events.arc_categoryDeviceGroup != '/IDS/Host/Antivirus'
     AND
         (
                  events.arc_type = 0
               OR events.arc_type IS NULL
         )
GROUP BY events.arc_deviceVendor,
         events.arc_deviceProduct,
         events.arc_deviceZoneName,
         events.arc_deviceAddress,
         events.arc_deviceHostName
ORDER BY SUM(events.arc_baseEventCount) DESC
```
#### Alert Counts by Type
```sql
SELECT   events.arc_categoryTechnique "Type",
         SUM(events.arc_baseEventCount) "Number of Events"
FROM     events
WHERE    events.arc_categoryDeviceGroup LIKE '/IDS%'
     AND events.arc_categoryDeviceGroup != '/IDS/Host/Antivirus'
     AND
         (
                  events.arc_type = 0
               OR events.arc_type IS NULL
         )
GROUP BY events.arc_categoryTechnique
ORDER BY SUM(events.arc_baseEventCount) DESC
```
#### Alert Counts per Hour
```sql
SELECT   HOUR(events.arc_endTime) "Hour",
         SUM(events.arc_baseEventCount) "Count",
         events.arc_deviceVendor "Vendor",
         events.arc_deviceProduct "Product"
FROM     events
WHERE    events.arc_categoryDeviceGroup LIKE '/IDS%'
     AND events.arc_categoryDeviceGroup != '/IDS/Host/Antivirus'
     AND
         (
                  events.arc_type = 0
               OR events.arc_type IS NULL
         )
GROUP BY HOUR(events.arc_endTime),
         events.arc_deviceVendor,
         events.arc_deviceProduct
ORDER BY HOUR(events.arc_endTime),
         SUM(events.arc_baseEventCount) DESC
```
#### Worm Infected System
```sql
SELECT   events.arc_sourceZoneURI "Source Zone",
         events.arc_sourceHostName "Source Host Name",
         events.arc_sourceAddress "Source Address"
FROM     events
WHERE    events.arc_categoryDeviceGroup LIKE '/IDS%'
     AND events.arc_categoryOutcome != '/Failure'
     AND
         (
                  UPPER(events.arc_categoryObject) = UPPER('/Host/Infection/Worm')
               OR UPPER(events.arc_categoryTechnique) = UPPER('/Code/Worm')
               OR UPPER(events.arc_categoryObject) = UPPER('/Vector/Worm')
         )
GROUP BY events.arc_sourceZoneURI,
         events.arc_sourceHostName,
         events.arc_sourceAddress
ORDER BY events.arc_sourceZoneURI,
         events.arc_sourceHostName,
         events.arc_sourceAddress
```
## Use Information
#### User Activity
```sql
SELECT  events.arc_sourceUserId "Source User ID",
        events.arc_sourceUserName "Source User",
        events.arc_destinationUserId "Destination User ID",
        events.arc_destinationUserName "Destination User",
        DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H:%i") "Time",
        events.arc_name "Event",
        events.arc_categoryOutcome "Result"
FROM    events
WHERE   events.arc_sourceUserId        IS NOT NULL
     OR events.arc_sourceUserName      IS NOT NULL
     OR events.arc_destinationUserId   IS NOT NULL
     OR events.arc_destinationUserName IS NOT NULL
GROUP BY events.arc_sourceUserId,
        events.arc_sourceUserName,
        events.arc_destinationUserId,
        events.arc_destinationUserName,
        DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H:%i") DESC,
        events.arc_name,
        events.arc_categoryOutcome
ORDER BY events.arc_sourceUserId,
        events.arc_sourceUserName,
        events.arc_destinationUserId,
        events.arc_destinationUserName,
        DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H:%i") DESC,
        events.arc_name,
        events.arc_categoryOutcome
```
#### Failed Logins
```sql
SELECT
    DATE_FORMAT(events.arc_endTime,"%Y-%m-%e %H:00") "Hour",
    SUM(events.arc_baseEventCount) "Count"
FROM events
WHERE
    events.arc_categoryBehavior = '/Authentication/Verify'
AND
    events.arc_categoryOutcome  = '/Failure'
GROUP BY
    DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H")
ORDER BY
    DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H") ASC,
    SUM(events.arc_baseEventCount)
```
#### Users by Connection Count
```sql
SELECT events.arc_destinationUserName "User Name",
  events.arc_destinationUserId "User ID",
  events.arc_destinationZoneURI "Zone",
  events.arc_destinationAddress "Address",
  events.arc_destinationHostName "Host Name",
  SUM(events.arc_baseEventCount) "Count"
FROM events
WHERE events.arc_categoryDeviceGroup LIKE '/Identity Management%'
AND
  (
    events.arc_categoryBehavior  = '/Access/Start'
  OR events.arc_categoryBehavior = 'Authentication/Verify'
  OR events.arc_categoryBehavior = '/Authorization/Verify'
  )
AND
  (
    events.arc_destinationUserName IS NOT NULL
  OR events.arc_destinationUserId IS NOT NULL
  )
GROUP BY events.arc_destinationUserName,
  events.arc_destinationUserId,
  events.arc_destinationZoneURI,
  events.arc_destinationAddress,
  events.arc_destinationHostName
ORDER BY SUM(events.arc_baseEventCount) DESC,
  events.arc_destinationUserName,
  events.arc_destinationUserId,
  events.arc_destinationZoneURI,
  events.arc_destinationAddress,
  events.arc_destinationHostName
```
#### User Administration
```sql
SELECT   events.arc_destinationZoneURI "Destination Zone",
         events.arc_destinationAddress "Destination Address",
         events.arc_destinationHostName "Destination Host Name",
         events.arc_endTime "End Time",
         events.arc_name "Event name",
         events.arc_categoryBehavior "Action",
         events.arc_sourceUserName "Administrator Name",
         events.arc_sourceUserId "Administrator ID",
         events.arc_destinationNtDomain "Windows Domain",
         events.arc_destinationUserName "User Name",
         events.arc_destinationUserId "User ID"
FROM     events
WHERE    events.arc_categoryDeviceGroup = '/Operating System'
     AND events.arc_categoryOutcome = '/Success'
     AND
(
events.arc_categoryBehavior = '/Authentication/Add'
OR events.arc_categoryBehavior = '/Authentication/Delete'
OR events.arc_categoryBehavior = '/Authentication/Modify'
)
ORDER BY events.arc_endTime
```
#### Accounts Created by User Account
```sql
SELECT  events.arc_endTime "Date",
        events.arc_destinationUserName "Destination User Name",
        events.arc_sourceUserName "Source User Name",
        events.arc_name "Name",
        events.arc_destinationZoneURI "Destination Zone Name",
        events.arc_destinationHostName "Destination Host Name"
FROM    events
WHERE   events.arc_categoryBehavior = '/Authentication/Add'
    AND events.arc_categoryOutcome   = '/Success'
ORDER BY UPPER(events.arc_destinationUserName) DESC,
        UPPER(events.arc_sourceUserName) DESC,
        events.arc_endTime DESC
```
#### Accounts Deleted by User Account
```sql
SELECT  events.arc_endTime "Date",
        events.arc_destinationUserName "Destination User Name",
        events.arc_sourceUserName "Source User Name",
        events.arc_name "Name",
        events.arc_destinationZoneURI "Destination Zone Name",
        events.arc_destinationHostName "Destination Host Name"
FROM    events
WHERE   events.arc_categoryBehavior = '/Authentication/Delete'
    AND events.arc_categoryOutcome   = '/Success'
ORDER BY UPPER(events.arc_destinationUserName) DESC,
        UPPER(events.arc_sourceUserName) DESC,
        events.arc_endTime DESC
```
#### Password Changes
```sql
SELECT  DATE_FORMAT(events.arc_endTime,"%Y-%m-%e %H:%i") "Time",
        events.arc_destinationUserName "Destination User Name",
        events.arc_destinationZoneURI "Destination Zone Name",
        events.arc_destinationAddress "Destination Address",
        events.arc_sourceZoneURI "Source Zone Name",
        events.arc_sourceAddress "Source Address"
FROM    events
WHERE   UPPER(events.arc_name) like UPPER('%password%')
    AND events.arc_categoryBehavior = '/Authentication/Modify'
    AND events.arc_categoryOutcome  = '/Success'
GROUP BY DATE_FORMAT(events.arc_endTime,"%Y-%m-%e %H:%i"),
        events.arc_destinationUserName,
        events.arc_destinationZoneURI,
        events.arc_destinationAddress,
        events.arc_sourceZoneURI,
        events.arc_sourceAddress
ORDER BY DATE_FORMAT(events.arc_endTime,"%Y-%m-%e %H:%i"),
        events.arc_destinationUserName,
        events.arc_destinationZoneURI,
        events.arc_destinationAddress,
        events.arc_sourceZoneURI,
        events.arc_sourceAddress
```
## Anti-Virus
#### Failed AV Updates
```sql
SELECT  events.arc_deviceVendor "Device Vendor",
        events.arc_deviceProduct "Device Product",
        events.arc_destinationZoneURI "Destination Zone Name",
        events.arc_destinationHostName "Destination Host Name",
        events.arc_destinationAddress "Destination Address",
        DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H:%i") "Time"
FROM    events
WHERE   events.arc_categoryBehavior = '/Modify/Content'
    AND events.arc_categoryDeviceGroup like '/IDS/Host/Antivirus%'
    AND events.arc_categoryObject  = '/Host/Application'
    AND events.arc_categoryOutcome = '/Failure'
ORDER BY UPPER(events.arc_deviceVendor),
        UPPER(events.arc_deviceProduct),
        UPPER(events.arc_destinationZoneURI),
        DATE_FORMAT(events.arc_endTime,"%Y-%m-%e-%H:%i") DESC
```
#### AV Updates (All)
```sql
SELECT  events.arc_destinationZoneURI "Destination Zone Name",
        events.arc_destinationHostName "Destination Host Name",
        events.arc_destinationAddress "Destination Address",
        events.arc_deviceVendor "Device Vendor",
        events.arc_deviceProduct "Device Product",
        events.arc_categoryOutcome "Outcome",
        sum(events.arc_baseEventCount) "Count"
FROM    events
WHERE   events.arc_categoryBehavior   = '/Modify/Content'
    AND events.arc_categoryDeviceGroup = '/IDS/Host/Antivirus'
    AND events.arc_categoryObject      = '/Host/Application'
GROUP BY events.arc_destinationZoneURI,
        events.arc_destinationHostName,
        events.arc_destinationAddress,
        events.arc_deviceVendor,
        events.arc_deviceProduct,
        events.arc_categoryOutcome
ORDER BY UPPER(events.arc_destinationZoneURI),
        UPPER(events.arc_destinationHostName),
        sum(events.arc_baseEventCount) DESC
```
## Custom
#### Common Account Login Failures
```sql
SELECT
events.arc_deviceProduct "Product",
events.arc_deviceVendor "Vendor",
events.arc_deviceAddress "Device Address",
events.arc_deviceHostName "Device Host",
SUM(events.arc_baseEventCount) "Event Count"
  
FROM events where arc_deviceVendor != "ArcSight"
GROUP BY arc_deviceProduct, arc_deviceVendor, arc_deviceAddress, arc_deviceHostName
ORDER BY SUM(arc_baseEventCount) DESC
```
#### Web Traffic Reporting - All URLs visited by ALL users (1 minute)
```sql
SELECT  events.arc_sourceZoneURI "Source Zone",
        events.arc_sourceAddress "Source Address",
        events.arc_sourceHostName "Source Host Name",
        events.arc_destinationPort "Destination Port",
        SUM(events.arc_baseEventCount) "Count"
FROM    events
WHERE events.arc_destinationPort IN (<%webPorts%>)
    AND events.arc_bytesOut        > 0
GROUP BY events.arc_sourceZoneURI,
        events.arc_sourceAddress ,
        events.arc_sourceHostName,
        events.arc_destinationPort
ORDER BY SUM(events.arc_baseEventCount) DESC,
        events.arc_sourceZoneURI,
        events.arc_sourceAddress ,
        events.arc_sourceHostName,
        events.arc_destinationPort
```
