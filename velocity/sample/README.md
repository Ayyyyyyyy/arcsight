
# ArcSight_Velocity
#### Additional resources
+ http://velocity.apache.org/engine/1.7/user-guide.html#variables
+ http://docs.oracle.com/javase/7/docs/api/java/lang/String.html

## Sample velocity code

### Customer URI check/replace
```javascript
#if($deviceHostName.endsWith("test.com"))/AllCustomers/TEST#elseif($agentHostName.endsWith("test.com"))/All Customers/CORP#end
```
### Report Folder Structure
###### Report Name: /2016/05/28/NameofReport_05-08-2016-14:00:00.pdf
```javascript
#set( $cyear=${CurrentMonth} )$cyear.substring(3)/#set( $cmnth=${CurrentMonth} )$cmnth.substring(0,2)/#set( $cday=${Today} )$cday.substring(3,5)/${ReportName}_${Now}
```
###### Other Options
+ `#set( $cyear=${CurrentMonth} )$cyear.substring(3) = Year`
+ `#set( $cmnth=${CurrentMonth} )$cmnth.substring(0,2) = Month`
+ `#set( $cday=${Today} )$cday.substring(3,5) = Day`

### Custom columns example
#####(Note: only tested in active channels and lists - testing cases next.)
```javascript
###########################
## Custom Velocity Template
## Creator: m. DeVries
## v 1.0
## Date: 3.3.17
###########################
##
## Colors
##
#set( $iRed = "#CD5C5C")
#set( $lSalmon = "#FFA07A")
#set( $dSalmon = "#E9967A")
#set( $crl = "#F08080")
##
## Variables
##
#set( $sIP = $sourceAddress)
#set( $dIP = $destinationAddress)
#set( $MRT = "$managerReceiptTime")
#set ($dHost = $deviceHostName.toUpperCase())
<html>
<!-- MRT: TimeStamp | sourceAddress | destinationAddress | hostname | Name -->
    <span>
        <strong><font color="$red">MRT: </font></strong> $MRT  |
        <strong><font color="$iRed"> $sIP </font></strong> | 
        <strong><font color="$crl"> $dIP </font></strong> |
        <strong><font color="$lSalmon"> hostnamehere </font></strong> |
        $name 
    </span>
</html>
```
#### Example
![alt text](https://github.com/Ayyyyyyyy/arcsight/blob/master/img/velocity_example1.png "velocity_example1.png")



