# How connect MikroTik routers to Mqtt broker
## original document https://help.mikrotik.com/docs/spaces/ROS/pages/46759978/MQTT

* Install the iot package, from the System/Packages menu.
![install package](img/1_packages.png)
* The installation will restart the router.
* In the IoT/MQTT menu add a broker.
![adding broker](img/2_broker.png)
* If your broker has SSL and a cert root signed SSL certificate you can enable it, but you need trust the root certificates for many common certificate authorities in the System/certificate/settings menu.
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
:local topic "microtik/firewallname"
:put ("[*] Gathering system info...")
:local cpuLoad [/system resource get cpu-load]
:local freeMemory [/system resource get free-memory]
:local usedMemory ([/system resource get total-memory] - $freeMemory)
:local rosVersion [/system package get value-name=version  [/system package find where name ~ "^routeros"]]
:local model [/system routerboard get value-name=model]
:local serialNumber [/system routerboard get value-name=serial-number]
:local upTime [/system resource get uptime]

:local m 1048576

:local WANtx [/interface/get WAN tx-byte]
:local WANrx [/interface/get WAN rx-byte]
:local LANtx [/interface/get LAN tx-byte]
:local LANrx [/interface/get LAN rx-byte]
:local DEMILtx [/interface/get DEMIL tx-byte]
:local DEMILrx [/interface/get DEMIL rx-byte]
:local BOXtx [/interface/get BOX tx-byte]
:local BOXrx [/interface/get BOX rx-byte]
:local wg1tx [/interface/get wg1 tx-byte]
:local wg1rx [/interface/get wg1 rx-byte]

#################################### MQTT #####################################
:local message \
"{\
    \"location\":\"whereisit\",\
    \"model\":\"$model\",\
    \"sn\":\"$serialNumber\",\
    \"ros\":\"$rosVersion\",\
    \"cpu\":$cpuLoad,\
    \"umem\":$usedMemory,\
    \"fmem\":$freeMemory,\
    \"uptime\":\"$upTime\",\
    \"WANtx\":$WANtx,\
    \"WANrx\":$WANrx,\
    \"LANtx\":$LANtx,\
    \"LANrx\":$LANrx,\
    \"DEMILtx\":$DEMILtx,\
    \"DEMILrx\":$DEMILrx,\
    \"BOXtx\":$BOXtx,\
    \"BOXrx\":$BOXrx,\
    \"wg1tx\":$wg1tx,\
    \"wg1rx\":$wg1rx\
}"

#:log info "$message";
:put ("[*] Total message size: $[:len $message] bytes")
:put ("[*] Sending message to MQTT broker...")
iot mqtt publish broker=$broker topic=$topic message=$message
:put ("[*] Done")
# for debug purpose
#:put $message
```

# Telegraf









