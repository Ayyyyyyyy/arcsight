# Validation of content using CAKe (CEF Army Knife experiment)
### Resources
+ [CAKe Info] (https://www.protect724.hpe.com/blogs/gaetan/2014/02/13/a-piece-of-cake)
+ [CAKe GIT] (https://github.com/gaetan-cardinal/CAKe)
+ All Credit for CAKe goes to Gaetan (cardinal_gaetan |at| yahoo.fr) - Thank you for all of your hard work!

## Use Cases

+ Testing rules 
+ Replay Events
+ Creating Custom Events
+ See cake info link above for more information

### Sample Cake File

```javascript
# Test for 
# Test By: Matt D.
# Date: 3.13.17
 
# CEF:0|CISCO|IronPort Web Security Appliance||403|- 443 TCP_DENIED_SSL|2|act=- 443 TCP_DENIED_SSL cs1=Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko cs1Label=Referer cs2=cs2data cs2Label=CS2 cs3=cs3data cs3Label=CS3 cs4=cs4data cs4Label=CS4 cs5Label=TEST  cs5=Cake Tool! cs6=cs6data cs6Label=CS6 src=10.1.81.72 dst=199.99.99.138 priority=0 suid=FAKEUSER request=https://www.google.es:443/search requestMethod=GET dpt=443 duser=MS\FAKEUSER@FAKESAUCEDOTCOM
 
 
== SECTION 1 : Global parameters
separator:,
 
== SECTION 2 : Header (required)
cefversion:0
devicevendor:CISCO
deviceproduct:IronPort Web Security Appliance
deviceversion:
signatureid:403
name:- 443 TCP_DENIED_SSL
severity:2
 
== SECTION 3 : Optional CEF fields
act:- 443 TCP_DENIED_SSL
cs1:Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko
cs1Label:Referer
cs2:cs2data
cs2Label:CS2
cs3:cs3data
cs3Label:CS3
cs4:cs4data
cs4Label:CS4
cs5Label:CAKE TEST
cs5:Cake Tool!
cs6:***testing1%%%testing2+++testing3|||testing4 (TEST for illegal chars - remove this line unless you want all cases to fail..)
cs6Label:CS6
_cefVer:0.1
src:[10.1.1.1:10.2.255.255]
dst:[199.99.99.99:199.99.99.255]
priority:0
suid:FAKEUSER
request:https://www.google.es:443/search
requestMethod:GET
dpt:443
duser:MS\FAKEUSER@FAKESAUCEDOTCOM
flexNumber1:2
flexString2:MDTest
#categoryOutcome:/Failure
#categorySignificance:/Informational/Warning
#categoryDeviceGroup:/Application
#categoryDeviceType:Web Filtering
```

### Implementation
1. Download and install cake
2. Ensure you have python 2.7 installed

#### Test
This test will read the __testcakefile.txt__ file, and forward events to syslogserver.  It also dumps the event output into the (you guessed it...) outputfile.txt. :thumbsup:

```javascript
python CAKe.py -g -f -n 1 -i testcakefile.txt -d -s syslogserver.server.com:514:u -o outputfile.txt
```
### More to come....
