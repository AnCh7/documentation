# Wireshark

Wireshark is a network packet analyzer. A network packet analyzer presents captured packet data in as much detail as possible.

Wireshark can capture traffic from many different network media types, including Ethernet, Wireless LAN, Bluetooth, USB, and more. The specific media types supported may be limited by several factors, including your hardware and operating system. An overview of the supported media types can be found at https://wiki.wireshark.org/CaptureSetup/NetworkMedia.

Wireshark can open packet captures from a large number of capture programs. Wireshark can save captured packets in many formats, including those used by other capture programs.

There are protocol dissectors (or decoders, as they are known in other products) for a great many protocols: https://www.wireshark.org/docs/dfref/

Wireshark will not manipulate things on the network, it will only “measure” things from it. Wireshark doesn’t send packets on the network or do other active things (except domain name resolution, but that can be disabled).

#### Filters

```
ssl && ip.addr==example.org
```
