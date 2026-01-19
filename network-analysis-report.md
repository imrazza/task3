# Network Analysis Report


---

## 1. IP Address & Interface Status

**Objective:** Identify active interfaces, assigned IP addresses, and interface states.

**Command:**
```bash
ip a
```

**Observations:**
- 1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet 10.255.255.254/32 brd 10.255.255.254 scope global lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1400 qdisc mq state UP group default qlen 1000
    link/ether 00:15:5d:c2:d5:50 brd ff:ff:ff:ff:ff:ff
    inet 172.25.63.150/20 brd 172.25.63.255 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::215:5dff:fec2:d550/64 scope link
       valid_lft forever preferred_lft forever
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:77:59:04:57 brd ff:ff:ff:ff:ff:ff
    inet 172.17.0.1/16 brd 172.17.255.255 scope global docker0
       valid_lft forever preferred_lft forever

---

## . Connectivity Testing 

**Objective:** Verify end-to-end connectivity to local and external endpoints.

**Commands & Results:**

Loopback:
```bash
ping -c 8.8.8.8
```
Result: PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=31.8 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=30.5 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=32.4 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=118 time=42.4 ms


---

## 3. DNS Resolution Testing

**Objective:** Test hostname resolution using system DNS.

**Commands:**
```bash
nslookup google.com
dig google.com
```

**Observations:**
Server:         10.255.255.254
Address:        10.255.255.254#53

Non-authoritative answer:
Name:   google.com
Address: 216.58.200.174
;; Got recursion not available from 10.255.255.254
Name:   google.com
Address: 2404:6800:4002:811::200e

**Conclusion:** DNS resolution is operational.

---

## 4. Network Path Tracing

**Objective:** Identify hop-by-hop routing path to an external destination.

**Command:**
```bash
traceroute google.com
```

**Observations (example):**
traceroute to google.com (216.58.200.174), 30 hops max, 60 byte packets
 1  LAPTOP-4ACTEH35.mshome.net (172.25.48.1)  2.660 ms  2.434 ms  2.322 ms
 2  192.168.1.1 (192.168.1.1)  5.270 ms  5.064 ms  4.951 ms
 3  * * *
 4  182.79.113.69 (182.79.113.69)  8.083 ms 182.79.106.137 (182.79.106.137)  7.994 ms 182.79.106.133 (182.79.106.133)  8.046 ms
 5  116.119.42.201 (116.119.42.201)  19.303 ms  19.248 ms 116.119.161.26 (116.119.161.26)  15.962 ms
 6  72.14.243.0 (72.14.243.0)  18.906 ms  15.140 ms  16.148 ms
 7  142.251.77.55 (142.251.77.55)  15.617 ms 142.251.239.247 (142.251.239.247)  17.539 ms 142.251.77.55 (142.251.77.55)  17.016 ms
 8  172.253.67.87 (172.253.67.87)  17.249 ms  19.871 ms 172.253.67.85 (172.253.67.85)  20.784 ms
 9  nrt12s11-in-f174.1e100.net (216.58.200.174)  19.266 ms  19.955 ms  19.029 ms

---

## 5. Service & Port Analysis

**Objective:** Identify listening services and bound ports.

**Command:**
```bash
ss -tulnp
```

**Findings (example):**
-Netid     State      Recv-Q     Send-Q          Local Address:Port          Peer Address:Port     Process
udp       UNCONN     0          0                  127.0.0.54:53                 0.0.0.0:*
udp       UNCONN     0          0               127.0.0.53%lo:53                 0.0.0.0:*
udp       UNCONN     0          0              10.255.255.254:53                 0.0.0.0:*
udp       UNCONN     0          0                   127.0.0.1:323                0.0.0.0:*
udp       UNCONN     0          0                       [::1]:323                   [::]:*
tcp       LISTEN     0          4096            127.0.0.53%lo:53                 0.0.0.0:*
tcp       LISTEN     0          4096               127.0.0.54:53                 0.0.0.0:*
tcp       LISTEN     0          1000           10.255.255.254:53                 0.0.0.0:*
tcp       LISTEN     0          50                          *:8080                     *:*

---

## 6. Failure Simulation

**Objective:** Observe impact of intentional network interface shutdown.

**Command:**
```bash
sudo ip link set eth0 down
```

**Effects:**
- Loss of ping responses
- DNS lookup failures due to lost connectivity

**Recovery:**
```bash
sudo ip link set eth0 up
```

## 9. Summary of Findings

- IP addressing and interface configuration valid
- Local and external connectivity verified
- DNS functioning as expected
- Route path intact to external servers
- Service exposure minimal and expected
- Failure simulation validates diagnostic behavior

---

