## Multi-Threading ##

#### Links:
[Caching Issues](https://www.protect724.hpe.com/groups/esm-performance/blog/2016/01/14/a-killer-option-that-will-force-your-connectors-to-get-rid-of-the-caching?commentID=14822%23comment-14822)

### Syslog parser multithreading
```
syslog.parser.threadcount=(2,4,8,16)
syslog.parser.multithreading.enabled=true
```

### Faster cache clearing
> NOTE: if you are noticing that your syslog Connector seems to be 'up but not doing anything' and has a cache, don't panic and act on that by trying to stop and start it again as it will only simply try to validate the cache count on each restart. If you truly want the Connector up ASAP and are carrying cache backlogs then this property will help.   

```
eventcache.scanforsize=false
http.transport.queuesize=8000
```

### Logger transport multithreading
```
transport.loggersecure.connection.persistent=true
transport.loggersecure.multithreaded=true
transport.loggersecure.threads=4
```

### ESM Transport multithreading
```
eventcache.scanforsize=false
http.transport.queuesize=8000
http.transport.multithreaded=true
http.transport.threadcount=4
```

## Event Cache

### If you have too much pre-processing cache, adjust these options:
```
agents[0].filequeuemaxfilecount=100
agents[0].filequeuemaxfilesize=10000000
syslog.parser.multithreading.enabled=true
syslog.parser.threadcount=4
```
### If you have too much post-processing cache, adjust these options:
 
##### For ESM destination:
```
http.transport.multithreaded=true
http.transport.threadcount=4
http.transport.queuesize=8000
eventcache.scanforsize=false
```

##### For Logger destination:
```
transport.loggersecure.multithreaded=true
transport.loggersecure.threads=4
eventcache.scanforsize=false
```
