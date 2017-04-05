# Velocity in ArcSight
### Resources
+ [Velocity Variables](http://velocity.apache.org/engine/1.7/user-guide.html#variables)
+ [Java Strings](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html)
+ [Java Regex Patterns](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html)
+ [Java Regex Testing](http://java-regex-tester.appspot.com/)
## Use Cases

#### Notifications - Velocity templates for email notifications in ESM.
Simple use of the velocity template notification vm's provided by arcsight.

#### Text Enhancement
This includes items like custom columns in active lists/channels, reports, and even overrides for the report archive folder.

#### Text Insertion
This includes utilizing velocity in a global or local variable to provide your case or rules additional data.  

# Notifications

### Velocity templates for email notifications in ESM
1. Create new directory in your ESM's notification directory (eg: `/opt/arcsight/manager/config/notification/custom`)
2. Navigate into the new directory, and create a new vm (or re-use newtemplate.vm) for a custom rule.
3. Customize the Email.vm template to reference the new template file you created in step 2.
4. All set - The manager DOES NOT require a restart when you make changes to these templates.

# Text Enhancement

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

### ArcSight Case Creation Template

#### This can be used to create a template for all cases created by arcsight.
+ Create a global variable and name it whatever you like
+ Select the **velocity template** argument
+ Insert the code below along with whatever other fields you'd like to pass along to each case.

**Note:** Make sure you aggregate this global variable and any other variables you plan to reference in each rule you want to use this in otherwise it won't work.

```javascript
## Custom velocity to display specific events in case text areas.
## Made by: Matt DeVries
## Date: 3.8.17
## Version: 1.0
## /// Set Variables ///
#set( $BSName = $customerName.toUpperCase())
#set( $BSURI = $customerURI)
#set( $eID = $eventId)
#set( $ent = $endTime)
#set( $sIP = $sourceAddress)
#set( $dIP = $destinationAddress)
#set( $sUser = $sourceUserName.toUpperCase())
#set( $mrt = $managerReceiptTime)
#set( $divider = "====================")
#set( $newline = "")
#set( $caseDetails = "// Case Details // ")
#set( $eventDetails = "// Event Details // ")
## /// Start Case Details ///
$caseDetails
Business Segment: $BSName
Business Unit URI: $customerURI
Use Case Display ID:
Use Case Name: $eID
Description of Alert:
$divider
$newline
$eventDetails
Manager Receipt Time: $mrt
Event Time: $ent
Device Vendor: $deviceVendor
Device Product: $deviceProduct
UserName: $sUser
Source Address: $sIP
Dest Address: $dIP
$divider
```
#### Example
![alt text](https://github.com/Ayyyyyyyy/arcsight/blob/master/img/velocity_case.png "velocity_case.png")
