# IP Addresses and DNS

## Learning goals

- Understand the importance of IP addresses in identifying devices connected to a network
- Know the differences between private/local and public/global IP addresses
- Learn what ports are and how they are used
- Understand the role `DNS` plays in the web
- Learn about `IPv4` and `IPv6`
- Learn how to use the `ping` command to test connections

## Introduction

The internet, broadly speaking, is a global network of computers and devices that connect with each other, allowing these devices to share information and interact in both simple and complex ways. That being said, in order to be able to interact with each other, we need ways of *identifying* and differentiating between these devices. It's like if you order something online, without an address to deliver (or return) to, there's no way to fulfill your order!

In this lesson we will be focusing on the main forms of identification computers use over the network and how they interact with each other.

## IP Addresses

An **IP address** (Internet Protocol Address) is a *unique* identifier assigned to every device connected to a network. Even though an IP address is unique to its network, we need to keep in mind that we are dealing with multiple networks simultaneously most of the time. Each computer connected to the internet has its own *local* or *private* network, governed by its router, as well as the *global* or *public* network, also known as the internet. In other words, *two* IP addresses. 

That might sound a bit confusing, but the main thing to keep in mind is that the IP addresses leased to computers on a local network are *not* accessible directly with that same local address from computers on the internet. The only way for that address to be interacted with by other computers, is for those other computers to also be connected to the same network.

The most commonly used version of IP addresses is what is known as `IPv4`-- a purely numerical address, consisting of four sets of numbers ranging from 0 to 255, separated by a period (`.`). An example of a common IP address used by routers is `192.168.1.1`. Another extremely important IP address to memorize is `127.0.0.1`, which can also be used as `localhost`, which is used to refer to the computer you are currently using.

Albeit short and sweet, `IPv4` has a very frustating limitation: a maximum of ~4.3 billion possible addresses (256^4). As a result, a successor was deemed an eventual necessity, and that's when `IPv6` came in.

`IPv6` uses longer addresses and hexadecimal (numbers and letters A-F) characters in order to provide space for future growth. An `IPv6` address is composed of *eight* groups of four characters. An example of an `IPv6` address is `2a03:2880:f12c:183:face:b00c::`. While not as easy or straightforward as `IPv4`, This range of values allows for a whopping 340 undecillion possible `IPv6` addresses. `IPv6` also offers more advantages than just the number of addresses, such as support for Quality of Service, but the main thing to keep in mind is that a device can have both types of IPs, and that they read differently.

## Finding your own IP

In order to find the *private/local* IP address of a *Linux* system (in this case, our virtual machine), you can use the `ifconfig` command. If your installation does not have the command yet available, you will be given a suggestion as to what package to install with `apt`; install it, and then run `ifconfig` again. 

Running `ifconfig` should yield some output similar to the following:

```bash
enpOsl: flags=4163<UP,BROADCAST,RUNNING,MULTICAST> mtu 1500 inet 192.168.64.7 netmask 255.255.255.0 broadcast 192.168.64.255 inet6 fe80::465:5bff:fe2e:2d32 prefixlen 64 scopeid 0x20<link> inet6 fd3d:eb53:38ee:ba3b:465:5bff:fe2e:2d32 prefixlen 64 scopeid Ox0<global> ether 06:65:5b:2e:2d:32 txqueuelen 1000 (Ethernet) RX packets 119 bytes 110163 (110.1 KB) RX errors 0 dropped 0 overruns 0 frame 0 TX packets 71 bytes 7137 (7.1 KB) TX errors 0 dropped 0 overruns 0 carrier 0 collisions 0
...
```

The bit that is of interest to us is `inet <ip>`; in this example, the local IP address of the device is `192.168.64.7`.

To find your *public/global* IP, you have to communicate with an external service. You can do this in a lot of different ways, but one that works from your commandline is the `dig` command:

```bash
$ dig +short txt ch whoami.cloudflare @1.0.0.1
"156.252.194.126"
```

This command communicates with *Cloudflare* to get data about itself (`whoami`). The `+short` parameter just shortens the output of `dig` so we don't get a lot of unnecessary info. `@1.0.0.1` is the IP of the *Cloudflare* server, and `TXT` is the *type* of request we are asking for. Don't worry too much about learning this command, as for this course it's just an example of how to get your public IP.

## Ports

Now that we know how addresses work, let's briefly look into *ports*. If the IP address is a house, you can think of ports as the people living in it. When a device sends data to another device, it includes the destination IP address, as well as the port. A **port** is simply a numerical endpoint for communication, represented by a number from `0` to `65535`, with the first `1024` ports reserved for specific services. Ports exist so that a device can have multiple processes listening simultaneously without interfering with each other.

Ports are commonly separated from the address with a single colon `:`. An example of a destination that includes both address and port is `127.0.0.1:8080`. The IP address in that example, `127.0.0.1`, refers to the local machine. The port, `8080`, is a number commonly used by some services, which is normally configurable. 

## DNS

Both IP addresses and ports make up the backbone of internet communication-- that being said, it's too much to request of users to remember the IP address of a server every time they want to connect with it. Let's say you want to go to *Google*; can you imagine instead of typing `google.com`, you had to type in `142.250.189.142`? Doesn't quite have the same ring to it. Not only that, but what if the server changes and gets a different IP address?

Enter *DNS* (Domain Name System). **DNS** is a distributed system that maps *domain names* to IP addresses. A **domain name** is simply a string of text that is used by DNS in order to map it to an IP address; for example, `google.com`, `speedtest.net`, etc. The owners of these domain names simply point their name to an underlying IP address, allowing users and other devices to access them simply using the domain name.

When a user enters a domain name into a browser, the browser sends a DNS query to a DNS server, which returns an IP address. The DNS server first checks its own cache to see if it has a record for the domain name:

![DNS with cache](https://curriculum-content.s3.amazonaws.com/6685/devops-m1-dns/dns_02.png)

If it does not, it forwards the request to another DNS server closer to the root. This process continues until a DNS server with a record for the domain name is found:

![DNS without cache](https://curriculum-content.s3.amazonaws.com/6685/devops-m1-dns/dns_01.png)

## `ping`

A frequently used command to test network connections is the `ping` command. `ping` sends a small packet of data to a network device, and then waits for a response, measuring the round-trip time the data took to both send and arrive.

To use the `ping` command, open up your terminal and type `ping <destination>`, where destination is either a URL/domain name or IP address:

```bash
$ ping google.com
PING google.com (172.217.3.78): 56 data bytes
64 bytes from 172.217.3.78: icmp_seq=0 ttl=119 time=45.473 ms
```

> **Note:** Like in most shell commands that remain running, you can use `ctrl-c` to end the process at any time.

As you can see, the address in the parenthesis following `google.com` is what `google.com`'s IP address ended up resolving to; that's DNS doing its job! The `time` parameter gives us how long it took for our request to *reach* that IP address + how long it took for our request to be *received* by our device.

## Conclusion

Understanding the basics of network identification is essential for interacting with the web (and the internet at wide). IP addresses, ports, and DNS play a vital role in this process, and in future chapters we will be putting this knowledge into practical use.
