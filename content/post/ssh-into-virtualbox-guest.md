+++
date = "2015-11-23T23:30:57-04:00"
tags = ["programming","ssh","virtualbox"]
title = "SSH into Virtualbox Guest"

+++

Use a NAT network interface and setup port forwarding like so:

* **Host IP:** 127.0.0.1
* **Host Port:** You decide
* **Guest IP:** 10.0.2.15
* **Guest Port:** 22

Run `ifconfig | grep inet` on the guest OS if the Guest IP above is not correct.
