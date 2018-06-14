## Introduction

MQTT is a machine-to-machine messaging protocol, designed to provide lightweight publish/subscribe communication to "Internet of Things" devices. It is commonly used for geo-tracking fleets of vehicles, home automation, environmental sensor networks, and utility-scale data collection.

Mosquitto is a popular MQTT server (or broker, in MQTT parlance) that has great community support and is easy to install and configure.


## Installation

### Binary Installation

Ubuntu 16.04 has a fairly recent version of Mosquitto in its default software repository. Log in with your non-root user and install Mosquitto with `apt`.

```
sudo apt install mosquitto mosquitto-clients
```

**By default, Ubuntu will start the Mosquitto service after install.** In addition, the mosquitto.service can be manipulated with the following commands after binary installation but not with source installation:

```
systemctl start mosquitto.service
systemctl stop mosquitto.service
...
```

### Source Installation (recommend)

1. `cd ~/Downloads/`
2. `wget http://mosquitto.org/files/source/mosquitto-1.5.tar.gz`
3. `tar -zxvf mosquitto-1.5.tar.gz`
4. `cd mosquitto-1.5/`
5. `sudo make install`
6. `ldconfig`
7. `cd examples/temperature_conversion/`
8. `make`
9. open four terminals
    - terminal 1 (start mosquitto.service): `mosquitto`
    - terminal 2 (run mqtt_temerature_conversion): `./mqtt_temerature_conversion`
    - terminal 4 (run mosquitto_sub): `mosquitto_sub -h localhost -t temperature/farenheit`
    - terminal 3 (run mosquitto_pub): `mosquitto_pub -h localhost -t temperature/celsius -m 100`

![](img/mosquitto/fig1.png?raw=true)

## mosquitto_sub & mosquitto_pub

*Topics* are labels that you publish messages to and subscribe to. They are arranged as a hierarchy, so you could have `sensors/outside/temp` and `sensors/outside/humidity`, for example. How you arrange topics is up to you and your needs. Throughout this tutorial we will use a simple test topic to test our configuration changes.

Log in to your server a second time, so you have two terminals side-by-side. In the new terminal, use `mosquitto_sub` to subscribe to the test topic:

```
mosquitto_sub -h localhost -t test
```

`-h` is used to specify the hostname of the MQTT server, and `-t` is the topic name. You'll see no output after hitting ENTER because `mosquitto_sub` is waiting for messages to arrive. Switch back to your other terminal and publish a message:

```
mosquitto_pub -h localhost -t test -m "hello world"
```

The options for `mosquitto_pub` are the same as `mosquitto_sub`, though this time we use the additional `-m` option to specify our message. Hit ENTER, and you should see hello world pop up in the other terminal. You've sent your first MQTT message!

Enter CTRL+C in the second terminal to exit out of `mosquitto_sub`.


## References

1. https://mosquitto.org/download/

2. http://mosquitto.org/files/source/

3. [How to Install and Secure the Mosquitto MQTT Messaging Broker on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-the-mosquitto-mqtt-messaging-broker-on-ubuntu-16-04)

4. [Mosquitto简要教程（安装/使用/测试）](https://blog.csdn.net/shagoo/article/details/7910598)

5. https://www.eclipse.org/mosquitto/man/libmosquitto-3.php

