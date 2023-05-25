  ![image](https://user-images.githubusercontent.com/87700008/229348597-d5ade815-54ed-4c18-9a53-7552a7eed613.png)

https://app.hackthebox.com/machines/536

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration :

### Port scan :

Started with the Rustscan & observed many open ports in the machine :

```bash
sudo rustscan -a 10.10.11.207 -- -sC -sV -vv -oN coder_nmap
```

![image](https://user-images.githubusercontent.com/87700008/229349067-8703b6e0-6fc8-41df-91e9-0a24a1379f64.png)

```c#
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2023-04-02 16:05:07Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: coder.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.coder.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc01.coder.htb
| Issuer: commonName=coder-DC01-CA/domainComponent=coder
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-06-30T04:24:26
| Not valid after:  2023-06-30T04:24:26
| MD5:   7a89 63c8 621c a1c1 a510 0013 50d8 6800
| SHA-1: 93e2 a8bd 85dc 5a87 67ff a4c1 3100 3634 d14d b0d7
| -----BEGIN CERTIFICATE-----
| MIIFzzCCBLegAwIBAgITPQAAAAOerCLLiaeZFgAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRUwEwYKCZImiZPyLGQBGRYFY29kZXIx
| FjAUBgNVBAMTDWNvZGVyLURDMDEtQ0EwHhcNMjIwNjMwMDQyNDI2WhcNMjMwNjMw
| MDQyNDI2WjAZMRcwFQYDVQQDEw5kYzAxLmNvZGVyLmh0YjCCASIwDQYJKoZIhvcN
| AQEBBQADggEPADCCAQoCggEBAMhIHCubKiPta6ZrmKu69L/cPXr7PZmuFET1zQ+8
| XVZajGemr9RBg4R36wjo4SQSXJatHcqBAg1ZlcQ/+yWC5YeSXGn0wydHEMk+XzLE
| cSAy79dH8zu3iaiKH2Eid2j0lq2i26o5ZjyNhJ5ZsUKL6J8EIGj5Mv41iARk9jTZ
| vIWpYgq4qXwENjNjyWmi/fSXoEA4uraGVt2XjMUuvJTseS5lMmKnsKDZgMkqPJlf
| MrGWlwRF8dofQVRPkKhm4AltlDDSJQxrPhkEXoWubwRwBY4esSR7t6Pw7dR+xzoL
| 2QPhPuhcZQUHAoQTMqVCYkbl41d+HkpT8rpRrePId5PL9s0CAwEAAaOCAuMwggLf
| MC8GCSsGAQQBgjcUAgQiHiAARABvAG0AYQBpAG4AQwBvAG4AdAByAG8AbABsAGUA
| cjAdBgNVHSUEFjAUBggrBgEFBQcDAgYIKwYBBQUHAwEwDgYDVR0PAQH/BAQDAgWg
| MHgGCSqGSIb3DQEJDwRrMGkwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3DQMEAgIA
| gDALBglghkgBZQMEASowCwYJYIZIAWUDBAEtMAsGCWCGSAFlAwQBAjALBglghkgB
| ZQMEAQUwBwYFKw4DAgcwCgYIKoZIhvcNAwcwHQYDVR0OBBYEFDWxdep4tkgRdB5t
| Ht1cey7wyE+ZMB8GA1UdIwQYMBaAFFdxD0h8hRoO9VxTc+rCS34BC/3QMIHGBgNV
| HR8Egb4wgbswgbiggbWggbKGga9sZGFwOi8vL0NOPWNvZGVyLURDMDEtQ0EsQ049
| ZGMwMSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2Vydmlj
| ZXMsQ049Q29uZmlndXJhdGlvbixEQz1jb2RlcixEQz1odGI/Y2VydGlmaWNhdGVS
| ZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBv
| aW50MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NO
| PWNvZGVyLURDMDEtQ0EsQ049QUlBLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2Vz
| LENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9Y29kZXIsREM9aHRiP2NB
| Q2VydGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9y
| aXR5MDoGA1UdEQQzMDGgHwYJKwYBBAGCNxkBoBIEEJx6JNHRqqRAgemVn6d/hC6C
| DmRjMDEuY29kZXIuaHRiMA0GCSqGSIb3DQEBCwUAA4IBAQCZJmFZVVQ6Zo4+FqMv
| Xdm2P3Sj23p3WiJxnYp3oULO3sUq7W6k9sv3leMewstbvRg+j4oPVPSzwhZcaJUs
| MBZBEN95/i10sU+bvzf76eMwUKdIld6TPsDFr069k75MgAXwClPBSmNHLLrt9HBL
| A2RWtqbOygYPG8+bqyS26NVbbRtdyr6HXQC6tvkDiaMRRBMG7WNuTSRqTP59lGdj
| VpDkuhhKPyIlyP8K4WUDgPjgWZuvCJH657ZDXHMo5eA1HS0ZU93jlcgLYnd0noS1
| obs7hQN1I4VCpCZjcwxJ5COPee2ITvyvhKvSqz+twU2BeDfFMJp01BtDzmt8UUDD
| MCH9
|_-----END CERTIFICATE-----
|_ssl-date: 2023-04-02T16:06:09+00:00; +6h32m49s from scanner time.
443/tcp   open  ssl/http      syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
| ssl-cert: Subject: commonName=default-ssl/organizationName=HTB/stateOrProvinceName=CA/countryName=US/organizationalUnitName=IT/localityName=Somewhere
| Issuer: commonName=coder-DC01-CA/domainComponent=coder
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-11-04T17:25:43
| Not valid after:  2032-11-01T17:25:43
| MD5:   e5fe a439 d835 6660 c2b7 78e5 78a1 244e
| SHA-1: 733c f457 1caa fdaa 8ad1 e8fb 0abc 6fec 7f93 2977
| -----BEGIN CERTIFICATE-----
| MIIF+TCCBOGgAwIBAgITPQAAAAwzA603A42YrQAAAAAADDANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRUwEwYKCZImiZPyLGQBGRYFY29kZXIx
| FjAUBgNVBAMTDWNvZGVyLURDMDEtQ0EwHhcNMjIxMTA0MTcyNTQzWhcNMzIxMTAx
| MTcyNTQzWjBfMQswCQYDVQQGEwJVUzELMAkGA1UECBMCQ0ExEjAQBgNVBAcTCVNv
| bWV3aGVyZTEMMAoGA1UEChMDSFRCMQswCQYDVQQLEwJJVDEUMBIGA1UEAxMLZGVm
| YXVsdC1zc2wwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCuB6vJzgGz
| Ko2ydVN+WcYifMry+pN5RL6X7ScFQGYxIS9DfJ428dTUEJ4KUWKL9Xsg/8UQ0k3w
| aP31dwgNzjrqYOc9ReB+6m4Y7ve+QfJ14qjimIfkATU8q0RHyacaN9gn6dXikGNO
| HiZ7hFex9xUzPFObJY76TEUO5wWFSpSjm4eDbkddlxQridmxBK06kfsKSkma4Lox
| WQebnvPLnJyFzV86J6vEz/+SN95ho24o/4WN/suMPyflsT02aB6Ud2KtbnW+Ih9V
| b7UWF0+9e9BrKb8dvXews0R+n4trvS6oLc/OJSW6MYJljAmlxMhsy6z73lHOB6oX
| pEB7hoiz/cG1AgMBAAGjggLHMIICwzAOBgNVHQ8BAf8EBAMCBaAwEwYDVR0lBAww
| CgYIKwYBBQUHAwEweAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYI
| KoZIhvcNAwQCAgCAMAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUD
| BAECMAsGCWCGSAFlAwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQU
| D/6uN0o4iHKh+GCuUBXW+/BP46owHwYDVR0jBBgwFoAUV3EPSHyFGg71XFNz6sJL
| fgEL/dAwgcYGA1UdHwSBvjCBuzCBuKCBtaCBsoaBr2xkYXA6Ly8vQ049Y29kZXIt
| REMwMS1DQSxDTj1kYzAxLENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNl
| cyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPWNvZGVyLERDPWh0Yj9j
| ZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlz
| dHJpYnV0aW9uUG9pbnQwgb0GCCsGAQUFBwEBBIGwMIGtMIGqBggrBgEFBQcwAoaB
| nWxkYXA6Ly8vQ049Y29kZXItREMwMS1DQSxDTj1BSUEsQ049UHVibGljJTIwS2V5
| JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1jb2Rl
| cixEQz1odGI/Y0FDZXJ0aWZpY2F0ZT9iYXNlP29iamVjdENsYXNzPWNlcnRpZmlj
| YXRpb25BdXRob3JpdHkwPAYJKwYBBAGCNxUHBC8wLQYlKwYBBAGCNxUI5Othg6rA
| DYWVkROFlrUWg46hO4FqhYiGHPWYHwIBZAIBAjAbBgkrBgEEAYI3FQoEDjAMMAoG
| CCsGAQUFBwMBMA0GCSqGSIb3DQEBCwUAA4IBAQAXsKApcaVqTZHhWq2oBz4+95G6
| 5aUC3FWDdH3c9EDoB6X/S+6wA/8dl45bcsuFW7pIGkv09endHj14hfdR/AKLozP/
| HTAQk3fj8oj5ARH2dT6Ygiwny3/lQWVTJsEbyhUZ2LgWtxonKpsPnNWY3VGLeicd
| knBpm1HGRS33tI6m+t55tI2NRRV8VjtXVe/qUVvPtrI6dOEGxUolm1d78j7WfYTz
| GkmPlqFKjfbs/X4O6NSNHCT4R8+J0uw5jqleVA9O/GNVB2whZDvW9VhNAgDkn9bu
| 5M6t0pTelGEfG152xUdpeYsTA8R790CKQar2m/Pak1HJ7yxaQ/+p1Uze1qR4
|_-----END CERTIFICATE-----
|_ssl-date: 2023-04-02T16:06:10+00:00; +6h32m48s from scanner time.
| tls-alpn: 
|_  http/1.1
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: coder.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.coder.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc01.coder.htb
| Issuer: commonName=coder-DC01-CA/domainComponent=coder
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-06-30T04:24:26
| Not valid after:  2023-06-30T04:24:26
| MD5:   7a89 63c8 621c a1c1 a510 0013 50d8 6800
| SHA-1: 93e2 a8bd 85dc 5a87 67ff a4c1 3100 3634 d14d b0d7
| -----BEGIN CERTIFICATE-----
| MIIFzzCCBLegAwIBAgITPQAAAAOerCLLiaeZFgAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRUwEwYKCZImiZPyLGQBGRYFY29kZXIx
| FjAUBgNVBAMTDWNvZGVyLURDMDEtQ0EwHhcNMjIwNjMwMDQyNDI2WhcNMjMwNjMw
| MDQyNDI2WjAZMRcwFQYDVQQDEw5kYzAxLmNvZGVyLmh0YjCCASIwDQYJKoZIhvcN
| AQEBBQADggEPADCCAQoCggEBAMhIHCubKiPta6ZrmKu69L/cPXr7PZmuFET1zQ+8
| XVZajGemr9RBg4R36wjo4SQSXJatHcqBAg1ZlcQ/+yWC5YeSXGn0wydHEMk+XzLE
| cSAy79dH8zu3iaiKH2Eid2j0lq2i26o5ZjyNhJ5ZsUKL6J8EIGj5Mv41iARk9jTZ
| vIWpYgq4qXwENjNjyWmi/fSXoEA4uraGVt2XjMUuvJTseS5lMmKnsKDZgMkqPJlf
| MrGWlwRF8dofQVRPkKhm4AltlDDSJQxrPhkEXoWubwRwBY4esSR7t6Pw7dR+xzoL
| 2QPhPuhcZQUHAoQTMqVCYkbl41d+HkpT8rpRrePId5PL9s0CAwEAAaOCAuMwggLf
| MC8GCSsGAQQBgjcUAgQiHiAARABvAG0AYQBpAG4AQwBvAG4AdAByAG8AbABsAGUA
| cjAdBgNVHSUEFjAUBggrBgEFBQcDAgYIKwYBBQUHAwEwDgYDVR0PAQH/BAQDAgWg
| MHgGCSqGSIb3DQEJDwRrMGkwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3DQMEAgIA
| gDALBglghkgBZQMEASowCwYJYIZIAWUDBAEtMAsGCWCGSAFlAwQBAjALBglghkgB
| ZQMEAQUwBwYFKw4DAgcwCgYIKoZIhvcNAwcwHQYDVR0OBBYEFDWxdep4tkgRdB5t
| Ht1cey7wyE+ZMB8GA1UdIwQYMBaAFFdxD0h8hRoO9VxTc+rCS34BC/3QMIHGBgNV
| HR8Egb4wgbswgbiggbWggbKGga9sZGFwOi8vL0NOPWNvZGVyLURDMDEtQ0EsQ049
| ZGMwMSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2Vydmlj
| ZXMsQ049Q29uZmlndXJhdGlvbixEQz1jb2RlcixEQz1odGI/Y2VydGlmaWNhdGVS
| ZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBv
| aW50MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NO
| PWNvZGVyLURDMDEtQ0EsQ049QUlBLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2Vz
| LENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9Y29kZXIsREM9aHRiP2NB
| Q2VydGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9y
| aXR5MDoGA1UdEQQzMDGgHwYJKwYBBAGCNxkBoBIEEJx6JNHRqqRAgemVn6d/hC6C
| DmRjMDEuY29kZXIuaHRiMA0GCSqGSIb3DQEBCwUAA4IBAQCZJmFZVVQ6Zo4+FqMv
| Xdm2P3Sj23p3WiJxnYp3oULO3sUq7W6k9sv3leMewstbvRg+j4oPVPSzwhZcaJUs
| MBZBEN95/i10sU+bvzf76eMwUKdIld6TPsDFr069k75MgAXwClPBSmNHLLrt9HBL
| A2RWtqbOygYPG8+bqyS26NVbbRtdyr6HXQC6tvkDiaMRRBMG7WNuTSRqTP59lGdj
| VpDkuhhKPyIlyP8K4WUDgPjgWZuvCJH657ZDXHMo5eA1HS0ZU93jlcgLYnd0noS1
| obs7hQN1I4VCpCZjcwxJ5COPee2ITvyvhKvSqz+twU2BeDfFMJp01BtDzmt8UUDD
| MCH9
|_-----END CERTIFICATE-----
|_ssl-date: 2023-04-02T16:06:10+00:00; +6h32m48s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: coder.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.coder.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc01.coder.htb
| Issuer: commonName=coder-DC01-CA/domainComponent=coder
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-06-30T04:24:26
| Not valid after:  2023-06-30T04:24:26
| MD5:   7a89 63c8 621c a1c1 a510 0013 50d8 6800
| SHA-1: 93e2 a8bd 85dc 5a87 67ff a4c1 3100 3634 d14d b0d7
| -----BEGIN CERTIFICATE-----
| MIIFzzCCBLegAwIBAgITPQAAAAOerCLLiaeZFgAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRUwEwYKCZImiZPyLGQBGRYFY29kZXIx
| FjAUBgNVBAMTDWNvZGVyLURDMDEtQ0EwHhcNMjIwNjMwMDQyNDI2WhcNMjMwNjMw
| MDQyNDI2WjAZMRcwFQYDVQQDEw5kYzAxLmNvZGVyLmh0YjCCASIwDQYJKoZIhvcN
| AQEBBQADggEPADCCAQoCggEBAMhIHCubKiPta6ZrmKu69L/cPXr7PZmuFET1zQ+8
| XVZajGemr9RBg4R36wjo4SQSXJatHcqBAg1ZlcQ/+yWC5YeSXGn0wydHEMk+XzLE
| cSAy79dH8zu3iaiKH2Eid2j0lq2i26o5ZjyNhJ5ZsUKL6J8EIGj5Mv41iARk9jTZ
| vIWpYgq4qXwENjNjyWmi/fSXoEA4uraGVt2XjMUuvJTseS5lMmKnsKDZgMkqPJlf
| MrGWlwRF8dofQVRPkKhm4AltlDDSJQxrPhkEXoWubwRwBY4esSR7t6Pw7dR+xzoL
| 2QPhPuhcZQUHAoQTMqVCYkbl41d+HkpT8rpRrePId5PL9s0CAwEAAaOCAuMwggLf
| MC8GCSsGAQQBgjcUAgQiHiAARABvAG0AYQBpAG4AQwBvAG4AdAByAG8AbABsAGUA
| cjAdBgNVHSUEFjAUBggrBgEFBQcDAgYIKwYBBQUHAwEwDgYDVR0PAQH/BAQDAgWg
| MHgGCSqGSIb3DQEJDwRrMGkwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3DQMEAgIA
| gDALBglghkgBZQMEASowCwYJYIZIAWUDBAEtMAsGCWCGSAFlAwQBAjALBglghkgB
| ZQMEAQUwBwYFKw4DAgcwCgYIKoZIhvcNAwcwHQYDVR0OBBYEFDWxdep4tkgRdB5t
| Ht1cey7wyE+ZMB8GA1UdIwQYMBaAFFdxD0h8hRoO9VxTc+rCS34BC/3QMIHGBgNV
| HR8Egb4wgbswgbiggbWggbKGga9sZGFwOi8vL0NOPWNvZGVyLURDMDEtQ0EsQ049
| ZGMwMSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2Vydmlj
| ZXMsQ049Q29uZmlndXJhdGlvbixEQz1jb2RlcixEQz1odGI/Y2VydGlmaWNhdGVS
| ZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBv
| aW50MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NO
| PWNvZGVyLURDMDEtQ0EsQ049QUlBLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2Vz
| LENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9Y29kZXIsREM9aHRiP2NB
| Q2VydGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9y
| aXR5MDoGA1UdEQQzMDGgHwYJKwYBBAGCNxkBoBIEEJx6JNHRqqRAgemVn6d/hC6C
| DmRjMDEuY29kZXIuaHRiMA0GCSqGSIb3DQEBCwUAA4IBAQCZJmFZVVQ6Zo4+FqMv
| Xdm2P3Sj23p3WiJxnYp3oULO3sUq7W6k9sv3leMewstbvRg+j4oPVPSzwhZcaJUs
| MBZBEN95/i10sU+bvzf76eMwUKdIld6TPsDFr069k75MgAXwClPBSmNHLLrt9HBL
| A2RWtqbOygYPG8+bqyS26NVbbRtdyr6HXQC6tvkDiaMRRBMG7WNuTSRqTP59lGdj
| VpDkuhhKPyIlyP8K4WUDgPjgWZuvCJH657ZDXHMo5eA1HS0ZU93jlcgLYnd0noS1
| obs7hQN1I4VCpCZjcwxJ5COPee2ITvyvhKvSqz+twU2BeDfFMJp01BtDzmt8UUDD
| MCH9
|_-----END CERTIFICATE-----
|_ssl-date: 2023-04-02T16:06:09+00:00; +6h32m48s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: coder.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc01.coder.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc01.coder.htb
| Issuer: commonName=coder-DC01-CA/domainComponent=coder
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-06-30T04:24:26
| Not valid after:  2023-06-30T04:24:26
| MD5:   7a89 63c8 621c a1c1 a510 0013 50d8 6800
| SHA-1: 93e2 a8bd 85dc 5a87 67ff a4c1 3100 3634 d14d b0d7
| -----BEGIN CERTIFICATE-----
| MIIFzzCCBLegAwIBAgITPQAAAAOerCLLiaeZFgAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRUwEwYKCZImiZPyLGQBGRYFY29kZXIx
| FjAUBgNVBAMTDWNvZGVyLURDMDEtQ0EwHhcNMjIwNjMwMDQyNDI2WhcNMjMwNjMw
| MDQyNDI2WjAZMRcwFQYDVQQDEw5kYzAxLmNvZGVyLmh0YjCCASIwDQYJKoZIhvcN
| AQEBBQADggEPADCCAQoCggEBAMhIHCubKiPta6ZrmKu69L/cPXr7PZmuFET1zQ+8
| XVZajGemr9RBg4R36wjo4SQSXJatHcqBAg1ZlcQ/+yWC5YeSXGn0wydHEMk+XzLE
| cSAy79dH8zu3iaiKH2Eid2j0lq2i26o5ZjyNhJ5ZsUKL6J8EIGj5Mv41iARk9jTZ
| vIWpYgq4qXwENjNjyWmi/fSXoEA4uraGVt2XjMUuvJTseS5lMmKnsKDZgMkqPJlf
| MrGWlwRF8dofQVRPkKhm4AltlDDSJQxrPhkEXoWubwRwBY4esSR7t6Pw7dR+xzoL
| 2QPhPuhcZQUHAoQTMqVCYkbl41d+HkpT8rpRrePId5PL9s0CAwEAAaOCAuMwggLf
| MC8GCSsGAQQBgjcUAgQiHiAARABvAG0AYQBpAG4AQwBvAG4AdAByAG8AbABsAGUA
| cjAdBgNVHSUEFjAUBggrBgEFBQcDAgYIKwYBBQUHAwEwDgYDVR0PAQH/BAQDAgWg
| MHgGCSqGSIb3DQEJDwRrMGkwDgYIKoZIhvcNAwICAgCAMA4GCCqGSIb3DQMEAgIA
| gDALBglghkgBZQMEASowCwYJYIZIAWUDBAEtMAsGCWCGSAFlAwQBAjALBglghkgB
| ZQMEAQUwBwYFKw4DAgcwCgYIKoZIhvcNAwcwHQYDVR0OBBYEFDWxdep4tkgRdB5t
| Ht1cey7wyE+ZMB8GA1UdIwQYMBaAFFdxD0h8hRoO9VxTc+rCS34BC/3QMIHGBgNV
| HR8Egb4wgbswgbiggbWggbKGga9sZGFwOi8vL0NOPWNvZGVyLURDMDEtQ0EsQ049
| ZGMwMSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2Vydmlj
| ZXMsQ049Q29uZmlndXJhdGlvbixEQz1jb2RlcixEQz1odGI/Y2VydGlmaWNhdGVS
| ZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBv
| aW50MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NO
| PWNvZGVyLURDMDEtQ0EsQ049QUlBLENOPVB1YmxpYyUyMEtleSUyMFNlcnZpY2Vz
| LENOPVNlcnZpY2VzLENOPUNvbmZpZ3VyYXRpb24sREM9Y29kZXIsREM9aHRiP2NB
| Q2VydGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9y
| aXR5MDoGA1UdEQQzMDGgHwYJKwYBBAGCNxkBoBIEEJx6JNHRqqRAgemVn6d/hC6C
| DmRjMDEuY29kZXIuaHRiMA0GCSqGSIb3DQEBCwUAA4IBAQCZJmFZVVQ6Zo4+FqMv
| Xdm2P3Sj23p3WiJxnYp3oULO3sUq7W6k9sv3leMewstbvRg+j4oPVPSzwhZcaJUs
| MBZBEN95/i10sU+bvzf76eMwUKdIld6TPsDFr069k75MgAXwClPBSmNHLLrt9HBL
| A2RWtqbOygYPG8+bqyS26NVbbRtdyr6HXQC6tvkDiaMRRBMG7WNuTSRqTP59lGdj
| VpDkuhhKPyIlyP8K4WUDgPjgWZuvCJH657ZDXHMo5eA1HS0ZU93jlcgLYnd0noS1
| obs7hQN1I4VCpCZjcwxJ5COPee2ITvyvhKvSqz+twU2BeDfFMJp01BtDzmt8UUDD
| MCH9
|_-----END CERTIFICATE-----
|_ssl-date: 2023-04-02T16:06:10+00:00; +6h32m48s from scanner time.
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49453/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49673/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49678/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49679/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49685/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49688/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49693/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
53156/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
53172/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6h32m48s, deviation: 0s, median: 6h32m47s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 33193/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 54439/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 53616/udp): CLEAN (Timeout)
|   Check 4 (port 17978/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2023-04-02T16:06:00
|_  start_date: N/A
```

In the port scan results I found 2 domains hosted on the machine : 

```
- Domain: coder.htb
- DNS: dc01.coder.htb
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

After the port scanning part I moved onto the sub-directory enumeration & DNS enumeration but didn't found anything from the scanning.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### SMB Enumeration :

My next step in SMB enumeration, to check the open file share & my access in those shares.

I found 3 shares with Read access : Developement, IPC$, Users

```bash
smbmap -u DoesNotExist -H coder.htb  # at -u we can use any random username, which may or may not exist in the domain.
```
![image](https://user-images.githubusercontent.com/87700008/229360716-33e73953-c86c-49af-b38f-05d924fe3d3a.png)


I then used "rid brute force" option from **CrackMapExec** which gave me list of the users & service accounts :

```bash
crackmapexec smb coder.htb -u "guest" -p "" --rid-brute
```

![image](https://user-images.githubusercontent.com/87700008/229478798-cdcd1681-0a8f-4771-b227-abef43f4a776.png)

With the gathered username I tried to perform AS-REP roasting but it seems kerberoas preauthication is enabled for all the users.😕

![image](https://user-images.githubusercontent.com/87700008/229482096-11911a6f-59dc-41e8-a7e3-eb422ec682a6.png)
