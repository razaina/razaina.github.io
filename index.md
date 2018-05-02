## Detecting IDA Pro debugging
k
IDA Pro packages pre-compiled binaries for remote debugging of different hosts as one can see below:

![Pre-compiled binaries for remote debugging](./images/dbgsrv_ida_pro.png)

So, basically if one wants to use IDA Pro for debugging a process running on a remote device, there are few steps that must be followed so IDA can properly instrument the debugging session. 

To make it short, here are the steps:
- push the right debug stub (*e.g*: android_server) on the target device
- execute the server (the debug stub) 
- on the host machine, the port that IDA Pro uses to communicate with the remote debug stub (namely, by default 23946), has to be forwarded
- From now on, one can debug a remote binary with IDA Pro

For more details, have a look at this [blogpost](https://finn.svbtle.com/remotely-debugging-android-binaries-in-ida-pro).

Consequently, an easy way to detect if IDA Pro's remote debug stub has been run would be by monitoring the opening of the TCP port 23946).

```c
#define IDAPRO_DEBUG_PORT "5D8A" //23946
#define BUFF_LEN 1024*4

char buff[BUFF_LEN];

FILE *fp;
const char dir[] = "/proc/net/tcp";
fp = fopen(dir, "r");
if(fp == NULL){
    return;
}
while(fgets(buff, BUFF_LEN, fp)){
    if(strstr(buff, IDAPRO_DEBUG_PORT) != NULL){
        /*
        Do stuff here...
        */
        fclose(fp);
        return;
    }
}
```

Missing:
- TracerPID:
  - https://github.com/strazzere/anti-emulator/blob/master/AntiEmulator/src/diff/strazzere/anti/debugger/FindDebugger.java#L36
  - https://github.com/razaina/anti-debug/blob/master/detect-debug/src/main/jni/jni_export.c#L195
  
- Has ADB in Emulator:
  - https://github.com/strazzere/anti-emulator/blob/master/AntiEmulator/src/diff/strazzere/anti/debugger/FindDebugger.java#L66
  

