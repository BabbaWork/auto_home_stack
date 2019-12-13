# auto home stack

another self-built home automation system

## introduction

My requirement was a robust, self-sufficient control system with which I could not depend on a proprietary system from a single manufacturer but could expand with hardware from different manufacturers.

Here my choice fell on Simatic CPUs. The sensors and actuators are wired to these and they process the control program.

Via the LAN interface, the CPUs communicate with a NAS running a data logger script. Here various data are stored in a mySQL database for later evaluation.

As a central control system I use NodeRed which provides a webserver for operation (and a lot of help during development).

## Requirements

requirement | feature
-|-
r|f

### robustnes

R: When switching on (e.g. after a power failure) the system itself should start up and be active.

R: Services / applications on servers should be monitored automatically and restarted if necessary.

R: Backups of databases and other artifacts should be created automatically.

### Interoperability

R: The different components of the system shall provide apis to exchange information and communicate with internal and external applications and systems.

R: The system shall be open to different transport protocols and publishing / describing interfaces.

F: For integrating iot devices the system provides a MQTT broker. That allows easy w-lan connecting of esp based devices.

F: The PLC are communicating to sensors / actors hardwired, via modbus, analog voltage signals, ...

R: The data formats, transport protocols and interfaces shall be derived from central managed masterdata

``` plantuml
@startuml

skinparam component {
    FontColor          black
    AttributeFontColor black
    FontSize           17
    AttributeFontSize  15
    AttributeFontname  Droid Sans Mono
    BackgroundColor    #6A9EFF
    BorderColor        black
    ArrowColor         #222266
}

title auto home stack
skinparam componentStyle uml2

node "user interface"{
    frame "deprecated"{
        [dotnet pc tool] #Gray
    }
    [dotnet pc tool] <--> udp_plc
    [web client] <..> http
}

cloud {
    interface www
    [AWS] --> www
    [netatmo] -> www
}

node "ahs" {
    interface data_logger as data_logger
    interface mqtt
    [data logger server] as dls
    [rpi_nodered] as nr

    dls -up-> [dashboard]
    [dashboard] -> http
    dls <-- data_logger
    www -> nr
    http <--> nr
    nr -right-> [plc_xx]
    nr <-> dls
    nr <-down-> mqtt
    udp_plc <-> [plc_xx]
    data_logger <- [plc_xx]
    [plc_xy] -up-> [plc_xx]
    [esp] <.up.> mqtt
    [sensor] -up-> [plc_xx]
    [aktor] <-up- [plc_xx]
}




@enduml
```

``` plantuml
@startuml
node "Legend"{
    package "device"{
        [item 1] as TMS #Tomato
    }
    folder "tst"{
      [item 2] as CM #Lime
    }
}
@enduml
```
