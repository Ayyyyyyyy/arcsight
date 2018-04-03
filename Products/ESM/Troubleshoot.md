## Content
- [Health](#-health)
    - [Case Creation Failure](#-case-creation-failure)

## [↑](#content) Health
#### [↑](#content) Case Creation Failure
1. Initial Case Creation Action: 
```bash
[2018-04-02 14:23:40,169][INFO ][default.com.arcsight.server.actions.ServerActionEngine] Making a new case 20180402 | RuleNameHere | 10.x.x.x | user | Spam;Business/Economy
```
2. Case Failure Action:
```bash
[2018-04-02 14:23:40,170][ERROR][default.com.arcsight.server.actions.ServerActionEngine] 
com.arcsight.common.resource.InvalidResourceNameException: Invalid resource name '20180402 | RuleNameHere | 10.x.x.x | user | Spam;Business/Economy' (Character '/' is not allowed)
	at com.arcsight.common.persist.ResourceBrokerBase._guardInvalidResourceNameOrAlias(ResourceBrokerBase.java:1655)
	at com.arcsight.common.persist.ResourceBrokerBase._guardInvalidResouceNameOrAlias(ResourceBrokerBase.java:1625)
	at com.arcsight.common.persist.CachingResourceBrokerBase.insertResources(CachingResourceBrokerBase.java:2296)
	at com.arcsight.common.persist.CachingResourceBrokerBase.insertResource(CachingResourceBrokerBase.java:2185)
	at com.arcsight.server.actions.ServerActionEngine.createCaseResource(ServerActionEngine.java:713)
	at com.arcsight.server.actions.ServerActionEngine.createNewCase(ServerActionEngine.java:451)
	at com.arcsight.server.actions.ServerActionEngine.fireAction(ServerActionEngine.java:928)
	at com.arcsight.rulesengine.actionengine.ActionCommandHandler$ActionCommandRunnable.run(ActionCommandHandler.java:287)
	at java.lang.Thread.run(Thread.java:745)
```
3. Grep for Events
```bash
grep 'InvalidResourceNameException' server.log.10
```
