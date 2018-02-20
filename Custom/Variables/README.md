# Velocity in ArcSight
### Resources
+ [Velocity Variables](http://velocity.apache.org/engine/1.7/user-guide.html#variables)
+ [Java Strings](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html)
+ [Java Methods](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html#method_summary)
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

#### Velocity Examples

|String Token|String Result|
|---|---|
|event.somestringfield=__BASE64Decod(“ZGVjb2RlZCBzdHJpbmc=”)|decoded string|
|event.somestringfield=__concatenate(“String1”,” “,”String2”,”FOO:”,TOKEN12)|String1String2FOO:stringintoken12|
|event.somestringfield=__concatenateDeleting()|NULL|
|event.somestringfield=__ConvertMSDNSURLOperation|NULL|
|event.somestringfield=__createRuleFiringInfo|NULL|
|event.somestringfield=__createTimeStampStringFromSecondsMicros(376126000,000)|2 Dec 1981 02:26:40 EST|
|event.sourceNTDomain=__extractNTDomain(“AMERICAS\WABC123”)|AMERICAS|
|event.sourceNTUser=__extractNTUser(“AMERICAS\WABC123”)|WABC123|
|event.soemstringfield=__extractProtocol(“TCP port 23”)|TCP|
|event.destinationHostName=__foundScanHostName(Token12)|Usually the hostname|
|event.somestringfield=__getCVEStringFor(CVEIdentifier)|where CVEIdentifier contains CVE-1999-0002 CVE CVE-1999-0002|
|event.somedomainfieldoftypeIPv6Address=__getIpV6AddressFromHighLow(“1,311,768,467,294,899,695”,”18,364,757,930,599,072,545”)|1234:5678:90AB:CDEF:FEDC:BA09:8765:4321|
|event.somestringfield=__getManhuntPriority(PriorityLong1,ReliabilityLong2)|(PriorityLong1 * Reliability-Long2)/256|
|event.somestringfield=__getNormalizedOS(stringtolookupinmapfileforOS)|AUP normalized OS String|
|event.somestringfield=__getProtocolName(23)|TELNET|
|event.somestringfield=__getProtocolNameFromString(23)|TELNET|
|event.somestringfield=__getSymantecNSPriority(SevLong1,ReliabilityLong2)|(SevLong1*ReliabilityLong2)/10|
|event.somestringfield=__getTimeZone(“-08:00”)|PST|
|event.somestringfield=__getTrendMicroHostName(hostnamevariationsfromTrendMicrostring)|hostname|
|event.somestringfield=__getTrendMicroUser(usernamevariationsfromTrendMicrostring)|username|
|event.somestringfield=__getVendor(“HP”)|same as the __stringConstant operation HP|
|event.somestringfield=__getVulnerabilityCategory(0\|1\|2\|3\|4)|“/scanner/device/vulnerability”for 0,“/scanner/device/openport”for 1“/scanner/device/user” for 2,“/scanner/device/banner” for 3,and“/scanner/device/uri” for 4|
|event.somestringfield=__getXForceStringFor(somestringparameter)|X-Forcesomestringparameter|
|event.somestringfield=__hexStringtoString(4865776c6574742d5061636b617264)|Hewlett-Packard|
|event.somestringfield=__hexStringToStringOperation(414243440D0A4546470D0A48494A)(notice embedded <cr> and <lf>)|ABCDEFGHIJ|
|event.somestringfield=__hexStringToStringOperation(4172635369676874)|ArcSight|
|event.somestringfield=__ifGreaterOrEqual(“101”,”100”,”Three”,”Four”)(if 1st string > 2nd string, then 3rd value, otherwise 4th value)|Three|
|event.somestringfield=__ifPositiveOperation(-5+7,”String1”,”String2”)|String1|
|event.somestringfield=__ifThenElse(in,”in”,”SAME”,”DIFFERENT”)|SAME|
|event.somestringfield=__longToDot4QuadAddress(16909060)|1.2.3.4|
|event.somestringfield=__longToStringOperation(LONGNUMBER) ..the string representation of a Long. If LONGNUMBER is 456123789 then the string representation of LONGNUMBER is returned.|456123789|
|event.somestringfield=__oneOf(Token12,””,”three”) (takes in arbitrary number of strings, the first one that is not null and not zero-length is returned)|three|
|event.somestringfield=__oneOfHostName(”1.2.3.4”,param2,…paramN) works like the oneOf function, but any parameter that looks like an IP Address is skipped.|param2|
|event.somestringfield=__oneOfNetBIOSName(arbitrary number of parameters) works like oneOf, but removes 1 or 2 \\ if present, before returning the result|stringwithoutleadingbackslashes|
|event.somestringfield=__regexToken(tokenorstringtoparse,”regextousetoparse”) event.sourceUserId=__regexToken(USER,”user\=(.*)\\s.*”) and USER token looks like this: somestringofletters;user=mrossi someotherstringofcharacters event.deviceCustomString2=__regexToken(SYSTEMINFO,”.*sessID\=([^\!]+)\!.*”)|mrossi Ps21Nw0HgHxf2kzvPnzDZ5QSj2Rp-BLVs67zV6dL00dhv1mxh71RK|
|event.somestringfield=__regexTokenFindAndJoin(timestamp,”(\\d+)”,,,)|NULL|
|event.somestringfield=__regexTokenNoWarning(tokentoparse,”regextoparsewith”)similar to regexToken, but regex has to match entire string, and if regex does not match then no warning is logged|NULL|
|event.somestringfield=__replaceAll(token12,”;”,” “)(starting string, regex, replacement string)|Replaces all semicolons in token12 with spaces|
|event.somestringfield=__replaceFirst(token12,”;”,” “)(starting string, regex, replacement string)|Replaces only the first semicolon in token12 with a space|
|event.somestringfield=__reverseDottedDecimalAddressByteOrder(Token12)If Token12 is a string of 1.2.3.4|4.3.2.1|
|event.somestringfield=__simpleMap(Token1,”chocolate=brown”,”vanilla=white”,”strawberry=pink”) ..and Token1 contains ‘strawberry’event.name=__simpleMap(acceptedrejected,”1=User Accepted”,”2=User Rejected”) …and acceptedrejected is the string ‘1’|pink User Accepted|
|event.somestringfield=__split(“The string to split”,” “,2) ..tested where “the string to split” is actually a token, not a literal string|string|
|event.deviceProduct=__stringConstant(“NameOfApplication”)|NameOfApplication|
|event.somestringfield=__stringTrim(“ some string with leading and or trailing blanks“)|some string with leading and or trailing blanks|
|event.somestringfield=__toHex(COUNT,8) if count is a long integer 65535 …|0x0000FFFF|
|event.somestringfield=__toLowerCase(Token4)|Whatever was in Token4, now in lower case|
|Event.somestringfield=__toUpperCase(Token5)|Whatever was in Token5, now in upper case|

|TimeStamp Token|Time Result|
|---|---|
|event.deviceCustomDate1=__createLocalTimeStampFromSecondsMicrosZone(SECONDS,MICROSEC,TIMEZ) some precision currently lost|NULL|
|event.deviceCustomDate1=__createLocalTimeStampFromSecondsSinceEpoch(DATETIME) wheretoken DATETIME contains 1303272501121|1303272501121000|
|event.deviceCustomDate1=__createLocalTimeStampFromGMTSecondsMillis(DATETIME,MSEC) DATETIMEis 1303272501121 and MSEC is 300|1303272501121300|
|event.deviceCustomDate1=__createLocalTimeStampFromGMTSecondsNanoseconds(SECONDS,NANOSEC) some precision currently lost|NULL|
|event.deviceCustomDate1=__createLocalTimeStampFromNanoSeconds(1234567890765432100) some precision currently lost|1234567890765|
|event.deviceCustomDate1=__createLocalTimeStampFromNTP()|NULL|
|event.deviceReceiptTime=__createOptionalTimeStampFromString(String2,yyyy-MM-ddHH\:mm\:ss.SSS)event.deviceCustomDate1=__createOptionalTimeStampFromString(__concatenate(Token0,”“,Token1),MM/dd/yyyy HH:mm:ss)|Where String2 is a String with a date in the specified format Using 2 Functions to concatenate a Date and a Time then convert to a date with the specified format|
|event.deviceCustomDate1=__createSafeLocalTimeStamp()|NULL|
|event.deviceCustomDate1=__createTimeStamp(DATE,TIME) The two tokens are a DATE and a TIME, both strings.|NULL
|event.deviceCustomDate1=__createTimeStampByHexEncodedTime(0000021efcde)|376126000|
|event.deviceCustomDate1=__createTimeStampByStartTimeElapsed(START,ELAPSED) where START is a string formatted as ddMMMyyyy hh:mm:ss and ELAPSED is a string formatted as hh:mm:ss. The result is the TimeStamp for the endTime=START+ELAPSED START is 11OCT2011 00:00:00 and ELAPSED is 5:55:55|1318326955000|
|event.deviceCustomDate1=__createTimeStampForOpsecStartTime()|NULL|
|event.deviceCustomDate1=__longToTimeStamp(GENERATEDTIME) where GENERATEDTIME is Long|NULL|
|event.deviceCustomDate1=__oneOfDateTime()|NULL|
|event.deviceCustomDate1=__parseMultipleTimeStamp()|NULL|
|event.deviceCustomDate1=__parseMutableTimeStamp()|NULL|
|event.deviceCustomDate1=__parseMutableTimeStampSilently()|NULL|
|event.deviceCustomDate1=__safeToDate()|NULL|
|event.deviceCustomDate1=__setYearToCurrentYear()|NULL|
|event.deviceCustomDate1=__useCurrentYear()|NULL|

|Long Token|Long Result|
|---|---|
|event.deviceCustomNumber1=__currentTimestampInSeconds() with no arguments…ignores any parameters, yes lowercase “s” in stamp Operation name, gives epoch seconds NOW|1307967249 <- the current epoch timestamp in seconds|
|event.fileSize=__hexStringToLong(fileSize) and fileSize is a String with value 0xABCDEF|11259375|
|event.deviceCustomNumber1=__hourMinuteSecondsToSeconds(HMS) … where HMS is a string token, such as “1:00:00” (no quotes inside the argument)|3600|
|event.deviceCustomNumber1=__integerToLong(123) …takes an Integer, returns a Long; null gets a null.|123|
|event.deviceCustomNumber1=__oneOfLong(,ABC,123)…takes in arbitrary number of Longs, the first one that is not null and not zero-length is returned|NULL|
|event.deviceCustomNumber1=__parsedSignedLong()|NULL|
|event.deviceCustomNumber1=__regexTokenAsLong()|NULL|
|event.deviceCustomNumber1=__safeToLong()|NULL|
|event.deviceCustomNumber1=__safeToRoundedLong()|NULL|
|event.deviceCustomNumber1=__splitAsLong()|NULL|
|event.deviceCustomNumber1=__toLongTimeStamp()|NULL|

|Integer Token|Integer Result|
|---|---|
|event.bytesIn=__divide(NUMERATOR,DENOMINATOR) …if NUMERATOR is 200 and DENOMINATOR is 3, result is an Integer event.bytesIn=__divide(NUMERATOR,DENOMINATOR) …if NUMERATOR is 200 and DENOMINATOR is 6, result is an Integer|67 33|
|__firstOfPositiveInteger(-1,3,5) takes integer arguments.|3|
|__getDeviceDirection(DIRECTION) ….if DIRECTION is a string of “in” or “inbound” then result is a zero …if DIRECTION is a string of “outbound” or “=>” then result is a 1|0 1|
|__getNotZeroPort(TOKEN) takes one TOKEN string argument. If the TOKEN is a null, or not a valid Integer, or zero, then result is NULL If the TOKEN is a valid non-zero Integer such as the string 123, then the Integer value is returned|NULL 123|
|__getOriginator(TOKEN) takes one TOKEN string argument. If the TOKEN is “Source” then the returned Integer is 0 If the TOKEN is “Destination” then the returned Integer is 1|0 1|
|__getOriginatorFromSourcePort|NULL|
|__getType(TOKEN) takes one TOKEN string argument, not sensitive to case. If the TOKEN is “correlation” or “correlated” then the returned Integer is 2 If the TOKEN is “aggregated” then the returned Integer is 1 If the TOKEN is anything else, then the returned Integer is 0|2 1 0|
|__integerConstant(TOKEN) takes one TOKEN Integer argument and that value is returned.
|__integerConstant(42)
|__integerConstant(42a) … If TOKEN is a literal string and NOT a valid Integer, thenNULL is returned.|42 NULL|
|__lengthOperation(TOKEN) takes one TOKEN string argument and returns the length of the string as an integer__lengthOperation(TOKEN) … where TOKEN is “CUPERTINO” then the integer returned is 9|9|
|__longToInteger|NULL|
|__oneOfInteger|NULL|
|__product|NULL|
|__regexTokenAsInteger|NULL|
|__safeToInteger|NULL|
|__splitAsInteger|NULL|
|__subtract(NUM4,NUM3) where NUM4 is 13 and NUM3 is 12|1|
|__sum(NUM3,NUM4) where NUM3 and NUM4 are Integers, say 12 and 13. Arguments are integer variables or string constants that are floating point values|25|

|IP Address Token|IP Address Result|
|---|---|
|event.destinationAddress=__doubleToAddress()|NULL|
|event.destinationAddress=__hexStringToAddress(HEXSTRING) where HEXSTRING is C0A80A0C event.destinationAddress=__hexStringToAddress(HEXSTRING) where HEXSTRING is c0a80a0b|3232238092 3232238091|
|event.destinationAddress=__noDot4QuadStringsToAddress(STR1,STR2,STR3,STR4) where STR1=192, STR2=168, STR3=10, and STR4=12 (all String types)|3232238092|
|event.destinationAddress=__noDotStringFormatToAddress(VAR) where VAR is 192168010012 and is a String type event.destinationAddress=__noDotStringFormatToAddress(VAR) where VAR is 001002003004 and is a String type|3232238092 16909060|
|event.destinationAddress=__numberToAddress(NUM) where NUM is 3232238092 and is a Long type 3232238092|3232238092|
|event.destinationAddress=__oneOfAddress(IPADDY) where IPADDY is 1.2.3.4 and is a String Type|16909060|
|event.destinationAddress=__regexTokenAsAddress(STRING2,” .?([0-9]+\\.[0-9]+\\.[0-9]+\\.[0-9]+).?”) where STRING2 is some string with an IP Address in the middle,such as “Joe with IP 192.168.10.12 logged in”|3232238092|
|event.destinationAddress=__signedNumberToAddress()|NULL|
|event.destinationAddress=__splitAsAddress(LARGE,\~,3) where LARGE is second~bar~192.168.10.12~|3232238092|

|Mac Address Token|MacAddress Result|
|---|---|
|event.destinationMacAddress=__getLongMACAddressByHexString(HEX3) … where HEX3 is a HEX string with no separators such as FFEEDDCCBBAA|281401388481450|
|event.destinationMacAddress=__getLongMACAddressByString(HEXSTRING) …where HEXSTRING is a string. For example, AA:BB:CC:DD:EE:FF or AA-BB-CC-DD-EE-FF. Separation of hex pairs with periods also works (undocumented)|187723572702975|
|event.destinationMacAddress=__oneOfMac(INT,STRING,HEXSTRING) similar to the __oneOf operation. Returns the first valid MAC address, or null if there aren’t any.|187723572702975|
