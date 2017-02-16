
# ArcSight_Velocity
#### Additional resources
+ http://velocity.apache.org/engine/1.7/user-guide.html#variables
+ http://docs.oracle.com/javase/7/docs/api/java/lang/String.html

### Sample velocity code

#### Customer URI check/replace
`#if($deviceHostName.endsWith("test.com"))/AllCustomers/TEST#elseif($agentHostName.endsWith("test.com"))/All Customers/CORP#end`

#### Report Folder Structure
###### Report Name: /2016/05/28/NameofReport_05-08-2016-14:00:00.pdf
`#set( $cyear=${CurrentMonth} )$cyear.substring(3)/#set( $cmnth=${CurrentMonth} )$cmnth.substring(0,2)/#set( $cday=${Today} )$cday.substring(3,5)/${ReportName}_${Now}`
###### Other Options
+ `#set( $cyear=${CurrentMonth} )$cyear.substring(3) = Year`
+ `#set( $cmnth=${CurrentMonth} )$cmnth.substring(0,2) = Month`
+ `#set( $cday=${Today} )$cday.substring(3,5) = Day`

