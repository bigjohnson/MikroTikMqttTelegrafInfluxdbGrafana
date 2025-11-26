# How connect MikroTik routers to Mqtt broker
## original document https://help.mikrotik.com/docs/spaces/ROS/pages/46759978/MQTT

* Install the iot package, from the System/Packages menu.
![install package](img/1_packages.png)
* The installation will restart the router.
* In the IoT/MQTT menu add a broker.
![adding broker](img/2_broker.png)
* If your broker has SSL you can enable it, but you need trust the root certificates for many common certificate authorities in the System/certificate/settings menu.
![enable ssl root certs](img/3_certificates.png)
* Test the mqtt broker connection publish something.
![test publish message](img/4_mqtt_publish.png)
![reading message](img/5_mqtt_receive.png)
* You can add a script to publish router info to the mqtt broker.
![create script](img/6_script.png)
* Run the script and get the result
![run script](img/7_run_script.png)

```
################################ Configuration ################################
# Name of an existing MQTT broker that should be used for publishing
:local broker "panu.it"
# MQTT topic where the message should be published
:local topic "javascript/mikrotic"
:put ("[*] Gathering system info...")
:local cpuLoad [/system resource get cpu-load]
:local freeMemory [/system resource get free-memory]
:local usedMemory ([/system resource get total-memory] - $freeMemory)
:local rosVersion [/system package get value-name=version  [/system package find where name ~ "^routeros"]]
:local model [/system routerboard get value-name=model]
:local serialNumber [/system routerboard get value-name=serial-number]
:local upTime [/system resource get uptime]

#################################### MQTT #####################################
:local message \
"{\"model\":\"$model\",\
    \"sn\":\"$serialNumber\",\
    \"ros\":\"$rosVersion\",\
    \"cpu\":$cpuLoad,\
    \"umem\":$usedMemory,\
    \"fmem\":$freeMemory,\
    \"uptime\":\"$upTime\"}"

:log info "$message";
:put ("[*] Total message size: $[:len $message] bytes")
:put ("[*] Sending message to MQTT broker...")
iot mqtt publish broker=$broker topic=$topic message=$message
:put ("[*] Done")
# for debug purpose
#:put $message
```








