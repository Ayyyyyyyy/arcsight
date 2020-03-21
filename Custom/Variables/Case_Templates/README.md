# Case Templates

## Case Automation - Automate Case details using a velocity template and global variable.
#### Use Case:  This example is checking for any time a user either deletes a rule or a filter (as seen in the lvCategory code below).
#### Event Flow: Events flow from tier 1 to tier 2 ESM - make sure you aggregate all fields required on tier 1 rules.

#### Velocity Details: 
1. The primary global variable will run through a list of known variables that you've set using the (you guessed it...) "#set" option.  
2. Once it reaches the end of the file, it will reference the local variable $lvCategory to check if the name matches whatever additional arguments you've added (in our case it's an #if/#elseIf argument).
3. if the event from the tier 1 ESM is associated with a rule or filter being removed, the template will display the additional "ARC" local variable information at the bottom of the case.  
4. If there was no rule/filter removed, the "FAIL" variable will be used (as seen in the below example).

#### How to Create it.
1. On the Tier 2 ESM, Select the option to create a global variable using the EvaluateVelocityTemplate option
2. Paste the code from below into the text box
3. Select the "Local Variables" tab and add the other 3 local variables ("arc, fail, lvcategory")
4. Assign the newly created global variable to a "Create New Case" action on a rule.
5. Ensure you're aggregating on all fields you plan to use in the template as well as the global variable itself. 

### gvCaseInfo
```javascript
## Name:        gvCaseInfo
## Version:     1.0
## Creator:     Matt DeVries
## 
### // Required Case Information //
##
#set( $rule = $name)
#set( $BSName = $customerName.toUpperCase())
#set( $BSURI = $customerURI)
#set( $eID = $eventId)
#set( $corr = $correlatedEventCount)
#set( $mssg = $message)
##
## -- Time Details
##
#set( $ET = $endTime)
#set( $MRT = $managerReceiptTime)
#set( $ENT = $endTime)
#set( $ART = $agentReceiptTime)
##
## -- Device Details
##
#set( $devHost = $deviceHostName)
#set( $devZName = $deviceZoneName)
#set( $devIP = $deviceAddress)
#set( $devDNS = $deviceDnsDomain)
#set( $DV = $deviceVendor)
#set( $DP = $deviceProduct)
#set( $dEventId = $deviceEventClassId)
#set( $dAction = $deviceAction)
##
## -- Host Details
##
#set( $sIP = $sourceAddress)
#set( $sHost = $sourceHostName)
#set( $sPort = $sourcePort)
#set( $sMAC = $sourceMacAddress)
#set( $sDNS = $sourceDnsDomain)
#set( $sNTDomain = $sourceNtDomain)
#set( $sZName = $sourceZoneName)
#set( $sTransIP = $sourceTranslatedAddress)
#set( $dIP = $destinationAddress)
#set( $dHost = $destinationHostName)
#set( $dPort = $destinationPort)
#set( $dMAC = $destinationMacAddress)
#set( $dDNS = $destinationDnsDomain)
#set( $dNTDomain = $destinationNtDomain)
#set( $dZName = $destinationZoneName)
#set( $dTransIP = $destinationTranslatedAddress)
##
## -- User Details
##
#set( $sUserName = $sourceUserName.toUpperCase())
#set( $sUserID = $sourceUserId.toUpperCase())
#set( $dUserName = $destinationUserName.toUpperCase())
#set( $dUserID = $destinationUserId.toUpperCase())
##
## -- Markdown
##
#set( $divider = "========================================")
#set( $newLine = "")
##
## // Start Case Details //
##
// ----- Case Details ----- //
Originating ESM: $flexNumber1
Business Segment: $BSName
Business Unit URI: $BSURI
Use Case Name: $rule
Description of Alert: $mssg
Originating eventID: $eID 
$newLine
// ----- Event Details ----- //
Manager Receipt Time: $MRT
Event Time: $ET
Correlated: $corr
Device Vendor: $DV  
Device Product: $DP
Device Host: $devHost
Device Address: $devIP
Device Class ID: $dEventId
Device Action: $dAction
$newLine
// ----- Host Details ----- //
Source Host: $sHost
Source Address: $sIP
Source Domain: $sDNS
Source User Name: $sUserName
Source UID: $sUserID
Source Port: $sPort
Source Zone: $sZName
$newLine
Dest. Host: $dHost
Dest. Address: $dIP
Dest. Domain: $dDNS
Dest. User: $dUserName
Dest. UID: $dUserID
Dest. Port: $dPort
Dest. Zone: $dZName
$divider
$newLine
$lvCategory
```
### ARC (1st Local Variable of gvCaseInfo)
```javascript
## Name:        ARC
## Version:     1.0
## Creator:     Matt DeVries
##
## // Resource Details //
##
#set( $fName = $fileName)
#set( $fPath = $filePath)
#set( $fType = $fileType)
// ----- Resource Details ----- //
Resource Name: $fName
Resource Type: $fType
Resource Path: $fPath
```

### FAIL (2nd Local Variable of gvCaseInfo)
```javascript
## Name:        FAIL
## Version:     1.0
## Creator:     Matt DeVries
## Note:        Can also be a GV - use 'alias' to reference...
……………………………………..________
………………………………,.-‘”……………….“~.,
………………………..,.-“……………………………..”-.,
…………………….,/………………………………………..”:,
…………………,?………………………………………………\,
………………./…………………………………………………..,}
……………../………………………………………………,:`^`..}
……………/……………………………………………,:”………/
…………..?…..__…………………………………..:`………../
…………./__.(…..”~-,_…………………………,:`………./
………../(_….”~,_……..”~,_………………..,:`…….._/
……….{.._$;_……”=,_…….”-,_…….,.-~-,},.~”;/….}
………..((…..*~_…….”=-._……”;,,./`…./”…………../
…,,,___.\`~,……”~.,………………..`…..}…………../
…………(….`=-,,…….`……………………(……;_,,-”
…………/.`~,……`-………………………….\……/\
………….\`~.*-,……………………………….|,./…..\,__
,,_……….}.>-._\……………………………..|…………..`=~-,
…..`=~-,_\_……`\,……………………………\
……………….`=~-,,.\,………………………….\
…………………………..`:,,………………………`\…………..__
……………………………….`=-,……………….,%`>–==“
…………………………………._\……….._,-%…….`\
……………………………..,<`.._|_,-&“…………….`
```

### lvCategory (3rd (Final) Local variable of gvCaseInfo)
```javascript
## Name:        lvCategory
## Version:     1.0
## Creator:     Matt DeVries
#if ($name == "Rule deleted" || $name == "Filter deleted")
$lvArc
#else 
$FAIL
#end
```
### Match
```javascript
// ----- Case Details ----- //
Originating ESM: 1234
Business Segment: CUSTOMERNAME
Business Unit URI: /All Customers/CUSTOMERNAME
Use Case Name: USECASE_NAME
Description of Alert: This alert identifies ETC ETC...
Originating eventID: 2305843

// ----- Event Details ----- //
Manager Receipt Time: 24 Mar 2017 18:01:40 UTC
Event Time: 24 Mar 2017 18:01:27 UTC
Correlated: 1
Device Vendor: CISCO  
Device Product: IronPort Web Security Appliance
Device Host: forwardingESM.domain.com
Device Address: 10.10.10.10
Device Class ID: 403
Device Action: - 443 TCP_DENIED_SSL

// ----- Host Details ----- //
Source Host: 
Source Address: 10.2.106.44
Source Domain: 
Source User Name: 
Source UID: TESTUSERNAME
Source Port: 
Source Zone: RFC1918: 10.0.0.0-10.255.255.255

Dest. Host: 
Dest. Address: 199.99.99.201
Dest. Domain: 
Dest. User: MS\FAKENAME@FAKEDOMAIN
Dest. UID: 
Dest. Port: 443
Dest. Zone: 198.51.101.0-199.255.255.255 (ARIN)
========================================

// ----- Resource Details ----- //
Resource Name: Categories
Resource Type: Rule
Resource Path: /All Rules/Real-time Rules/ISA/Stage/Categories
```
### No Match (Notice the "FAIL" Variable at the bottom populated)
```javascript
// ----- Case Details ----- //
Originating ESM: 1234
Business Segment: CUSTOMERNAME
Business Unit URI: /All Customers/CUSTOMERNAME
Use Case Name: USECASE_NAME
Description of Alert: This alert identifies ETC ETC...
Originating eventID: 2305843

// ----- Event Details ----- //
Manager Receipt Time: 24 Mar 2017 18:01:40 UTC
Event Time: 24 Mar 2017 18:01:27 UTC
Correlated: 1
Device Vendor: CISCO  
Device Product: IronPort Web Security Appliance
Device Host: forwardingESM.domain.com
Device Address: 10.10.10.10
Device Class ID: 403
Device Action: - 443 TCP_DENIED_SSL

// ----- Host Details ----- //
Source Host: 
Source Address: 10.2.106.44
Source Domain: 
Source User Name: 
Source UID: TESTUSERNAME
Source Port: 
Source Zone: RFC1918: 10.0.0.0-10.255.255.255

Dest. Host: 
Dest. Address: 199.99.99.201
Dest. Domain: 
Dest. User: MS\FAKENAME@FAKEDOMAIN
Dest. UID: 
Dest. Port: 443
Dest. Zone: 198.51.101.0-199.255.255.255 (ARIN)
========================================

……………………………………..________
………………………………,.-‘”……………….“~.,
………………………..,.-“……………………………..”-.,
…………………….,/………………………………………..”:,
…………………,?………………………………………………\,
………………./…………………………………………………..,}
……………../………………………………………………,:`^`..}
……………/……………………………………………,:”………/
…………..?…..__…………………………………..:`………../
…………./__.(…..”~-,_…………………………,:`………./
………../(_….”~,_……..”~,_………………..,:`…….._/
……….{.._$;_……”=,_…….”-,_…….,.-~-,},.~”;/….}
………..((…..*~_…….”=-._……”;,,./`…./”…………../
…,,,___.\`~,……”~.,………………..`…..}…………../
…………(….`=-,,…….`……………………(……;_,,-”
…………/.`~,……`-………………………….\……/\
………….\`~.*-,……………………………….|,./…..\,__
,,_……….}.>-._\……………………………..|…………..`=~-,
…..`=~-,_\_……`\,……………………………\
……………….`=~-,,.\,………………………….\
…………………………..`:,,………………………`\…………..__
……………………………….`=-,……………….,%`>–==“
…………………………………._\……….._,-%…….`\
……………………………..,<`.._|_,-&“…………….`
```
### Screenshot
![alt text](https://github.com/Ayyyyyyyy/arcsight/blob/master/img/gvCaseInfo.png "gvCaseInfo.png")
