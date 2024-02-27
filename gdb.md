# GDB

## Remote Debugging Via Serial

Serial interface needs to be configured as raw

```bash
$ stty -F /dev/ttyACM0 raw -echo -echoctl -echoke
```
