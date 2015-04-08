# Write script auto respawn

`cd /etc/init/`
<br>
Create file you want.
`vim <name>.conf`

Content follow this structure
```
description "Crawler"
exec /bin/bash /data/forum/dsvncrawler/_run.sh
respawn
```
Reload config
```
initctl reload-configuration
```

Control
```
initctl <start|stop|status> <name>
```
