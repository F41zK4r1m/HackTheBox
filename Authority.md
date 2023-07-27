![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/757bec12-b6cc-44f3-81f4-86bc2ba7f908)

https://app.hackthebox.com/machines/Authority

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

### Port scanning:

I began the enumeration with port & service scan using rustscan & found multiple open port running in the machine:

```bash
sudo rustscan -a 10.10.11.222 -- -sC -sV -vv -oN authority_nmap
```

```Rust
PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
| http-methods: 
|   Supported Methods: OPTIONS TRACE GET HEAD POST
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2023-07-19 19:04:29Z)
135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: othername:<unsupported>, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494 7710 6f6b 8100 e4e1 9cf2 aa40 dae1
| SHA-1: dded b994 b80c 83a9 db0b e7d3 5853 ff8e 54c6 2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBGMRQwEgYKCZImiZPyLGQBGRYEY29ycDETMBEGCgmSJomT8ixkARkWA2h0YjEZ
| MBcGA1UEAxMQaHRiLUFVVEhPUklUWS1DQTAeFw0yMjA4MDkyMzAzMjFaFw0yNDA4
| MDkyMzEzMjFaMAAwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDVsJL0
| ae0n8L0Eg5BAHi8Tmzmbe+kIsXM6NZvAuqGgUsWNzsT4JNWsZqrRoHMr+kMC4kpX
| 4QuOHTe74iyB8TvucgvwxKEi9uZl6C5unv3WNFhZ9KoTOCno26adxqKPbzS5KQtk
| ZCvQfqQKOML0DuzA86kwh4uY0SjVR+biRj4IkkokWrPDWzzow0gCpO5HNcKPhSTl
| kAfdmdQRPjkXQq3h2QnfYAwOMGoGeCiA1whIo/dvFB6T9Kx4Vdcwi6Hkg4CwmbSF
| CHGbeNGtMGeWw/s24QWZ6Ju3J7uKFxDXoWBNLi4THL72d18jcb+i4jYlQQ9bxMfI
| zWQRur1QXvavmIM5AgMBAAGjggLxMIIC7TA9BgkrBgEEAYI3FQcEMDAuBiYrBgEE
| AYI3FQiEsb4Mh6XAaYK5iwiG1alHgZTHDoF+hKv0ccfMXgIBZAIBAjAyBgNVHSUE
| KzApBgcrBgEFAgMFBgorBgEEAYI3FAICBggrBgEFBQcDAQYIKwYBBQUHAwIwDgYD
| VR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCQYHKwYBBQIDBTAMBgorBgEE
| AYI3FAICMAoGCCsGAQUFBwMBMAoGCCsGAQUFBwMCMB0GA1UdDgQWBBTE4oKGc3Jv
| tctii3A/pyevpIBM/TAfBgNVHSMEGDAWgBQrzmT6FcxmkoQ8Un+iPuEpCYYPfTCB
| zQYDVR0fBIHFMIHCMIG/oIG8oIG5hoG2bGRhcDovLy9DTj1odGItQVVUSE9SSVRZ
| LUNBLENOPWF1dGhvcml0eSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2Vydmlj
| ZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1odGIsREM9Y29ycD9j
| ZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlz
| dHJpYnV0aW9uUG9pbnQwgb8GCCsGAQUFBwEBBIGyMIGvMIGsBggrBgEFBQcwAoaB
| n2xkYXA6Ly8vQ049aHRiLUFVVEhPUklUWS1DQSxDTj1BSUEsQ049UHVibGljJTIw
| S2V5JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1o
| dGIsREM9Y29ycD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlm
| aWNhdGlvbkF1dGhvcml0eTBUBgNVHREBAf8ESjBIoCMGCisGAQQBgjcUAgOgFQwT
| QVVUSE9SSVRZJEBodGIuY29ycIISYXV0aG9yaXR5Lmh0Yi5jb3JwgghodGIuY29y
| cIIDSFRCMA0GCSqGSIb3DQEBCwUAA4IBAQCH8O6l8pRsA/pyKKsSSkie8ijDhCBo
| zoOuHiloC694xvs41w/Yvj9Z0oLiIkroSFPUPTDZOFqOLuFSDbnDNtKamzfbSfJR
| r4rj3F3r7S3wwK38ElkoD8RbqDiCHan+2bSf7olB1AdS+xhp9IZvBWZOlT0xXjr5
| ptIZERSRTRE8qyeX7+I4hpvGTBjhvdb5LOnG7spc7F7UHk79Z+C3BWG19tyS4fw7
| /9jm2pW0Maj1YEnX7frbYtYlO7iQ3KeDw1PSCMhMlipovbCpMJ1YOX9yeQgvvcg0
| E0r8uQuHmwNTgD5dUWuHtDv/oG7j63GuTNwEfZhtzR2rnN9Vf2IH9Zal
|_-----END CERTIFICATE-----
|_ssl-date: 2023-07-19T19:05:36+00:00; +4h00m00s from scanner time.
445/tcp   open  microsoft-ds? syn-ack ttl 127
464/tcp   open  kpasswd5?     syn-ack ttl 127
593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: othername:<unsupported>, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494 7710 6f6b 8100 e4e1 9cf2 aa40 dae1
| SHA-1: dded b994 b80c 83a9 db0b e7d3 5853 ff8e 54c6 2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBGMRQwEgYKCZImiZPyLGQBGRYEY29ycDETMBEGCgmSJomT8ixkARkWA2h0YjEZ
| MBcGA1UEAxMQaHRiLUFVVEhPUklUWS1DQTAeFw0yMjA4MDkyMzAzMjFaFw0yNDA4
| MDkyMzEzMjFaMAAwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDVsJL0
| ae0n8L0Eg5BAHi8Tmzmbe+kIsXM6NZvAuqGgUsWNzsT4JNWsZqrRoHMr+kMC4kpX
| 4QuOHTe74iyB8TvucgvwxKEi9uZl6C5unv3WNFhZ9KoTOCno26adxqKPbzS5KQtk
| ZCvQfqQKOML0DuzA86kwh4uY0SjVR+biRj4IkkokWrPDWzzow0gCpO5HNcKPhSTl
| kAfdmdQRPjkXQq3h2QnfYAwOMGoGeCiA1whIo/dvFB6T9Kx4Vdcwi6Hkg4CwmbSF
| CHGbeNGtMGeWw/s24QWZ6Ju3J7uKFxDXoWBNLi4THL72d18jcb+i4jYlQQ9bxMfI
| zWQRur1QXvavmIM5AgMBAAGjggLxMIIC7TA9BgkrBgEEAYI3FQcEMDAuBiYrBgEE
| AYI3FQiEsb4Mh6XAaYK5iwiG1alHgZTHDoF+hKv0ccfMXgIBZAIBAjAyBgNVHSUE
| KzApBgcrBgEFAgMFBgorBgEEAYI3FAICBggrBgEFBQcDAQYIKwYBBQUHAwIwDgYD
| VR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCQYHKwYBBQIDBTAMBgorBgEE
| AYI3FAICMAoGCCsGAQUFBwMBMAoGCCsGAQUFBwMCMB0GA1UdDgQWBBTE4oKGc3Jv
| tctii3A/pyevpIBM/TAfBgNVHSMEGDAWgBQrzmT6FcxmkoQ8Un+iPuEpCYYPfTCB
| zQYDVR0fBIHFMIHCMIG/oIG8oIG5hoG2bGRhcDovLy9DTj1odGItQVVUSE9SSVRZ
| LUNBLENOPWF1dGhvcml0eSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2Vydmlj
| ZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1odGIsREM9Y29ycD9j
| ZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlz
| dHJpYnV0aW9uUG9pbnQwgb8GCCsGAQUFBwEBBIGyMIGvMIGsBggrBgEFBQcwAoaB
| n2xkYXA6Ly8vQ049aHRiLUFVVEhPUklUWS1DQSxDTj1BSUEsQ049UHVibGljJTIw
| S2V5JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1o
| dGIsREM9Y29ycD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlm
| aWNhdGlvbkF1dGhvcml0eTBUBgNVHREBAf8ESjBIoCMGCisGAQQBgjcUAgOgFQwT
| QVVUSE9SSVRZJEBodGIuY29ycIISYXV0aG9yaXR5Lmh0Yi5jb3JwgghodGIuY29y
| cIIDSFRCMA0GCSqGSIb3DQEBCwUAA4IBAQCH8O6l8pRsA/pyKKsSSkie8ijDhCBo
| zoOuHiloC694xvs41w/Yvj9Z0oLiIkroSFPUPTDZOFqOLuFSDbnDNtKamzfbSfJR
| r4rj3F3r7S3wwK38ElkoD8RbqDiCHan+2bSf7olB1AdS+xhp9IZvBWZOlT0xXjr5
| ptIZERSRTRE8qyeX7+I4hpvGTBjhvdb5LOnG7spc7F7UHk79Z+C3BWG19tyS4fw7
| /9jm2pW0Maj1YEnX7frbYtYlO7iQ3KeDw1PSCMhMlipovbCpMJ1YOX9yeQgvvcg0
| E0r8uQuHmwNTgD5dUWuHtDv/oG7j63GuTNwEfZhtzR2rnN9Vf2IH9Zal
|_-----END CERTIFICATE-----
|_ssl-date: 2023-07-19T19:05:36+00:00; +4h00m00s from scanner time.
3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: othername:<unsupported>, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494 7710 6f6b 8100 e4e1 9cf2 aa40 dae1
| SHA-1: dded b994 b80c 83a9 db0b e7d3 5853 ff8e 54c6 2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBGMRQwEgYKCZImiZPyLGQBGRYEY29ycDETMBEGCgmSJomT8ixkARkWA2h0YjEZ
| MBcGA1UEAxMQaHRiLUFVVEhPUklUWS1DQTAeFw0yMjA4MDkyMzAzMjFaFw0yNDA4
| MDkyMzEzMjFaMAAwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDVsJL0
| ae0n8L0Eg5BAHi8Tmzmbe+kIsXM6NZvAuqGgUsWNzsT4JNWsZqrRoHMr+kMC4kpX
| 4QuOHTe74iyB8TvucgvwxKEi9uZl6C5unv3WNFhZ9KoTOCno26adxqKPbzS5KQtk
| ZCvQfqQKOML0DuzA86kwh4uY0SjVR+biRj4IkkokWrPDWzzow0gCpO5HNcKPhSTl
| kAfdmdQRPjkXQq3h2QnfYAwOMGoGeCiA1whIo/dvFB6T9Kx4Vdcwi6Hkg4CwmbSF
| CHGbeNGtMGeWw/s24QWZ6Ju3J7uKFxDXoWBNLi4THL72d18jcb+i4jYlQQ9bxMfI
| zWQRur1QXvavmIM5AgMBAAGjggLxMIIC7TA9BgkrBgEEAYI3FQcEMDAuBiYrBgEE
| AYI3FQiEsb4Mh6XAaYK5iwiG1alHgZTHDoF+hKv0ccfMXgIBZAIBAjAyBgNVHSUE
| KzApBgcrBgEFAgMFBgorBgEEAYI3FAICBggrBgEFBQcDAQYIKwYBBQUHAwIwDgYD
| VR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCQYHKwYBBQIDBTAMBgorBgEE
| AYI3FAICMAoGCCsGAQUFBwMBMAoGCCsGAQUFBwMCMB0GA1UdDgQWBBTE4oKGc3Jv
| tctii3A/pyevpIBM/TAfBgNVHSMEGDAWgBQrzmT6FcxmkoQ8Un+iPuEpCYYPfTCB
| zQYDVR0fBIHFMIHCMIG/oIG8oIG5hoG2bGRhcDovLy9DTj1odGItQVVUSE9SSVRZ
| LUNBLENOPWF1dGhvcml0eSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2Vydmlj
| ZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1odGIsREM9Y29ycD9j
| ZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlz
| dHJpYnV0aW9uUG9pbnQwgb8GCCsGAQUFBwEBBIGyMIGvMIGsBggrBgEFBQcwAoaB
| n2xkYXA6Ly8vQ049aHRiLUFVVEhPUklUWS1DQSxDTj1BSUEsQ049UHVibGljJTIw
| S2V5JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1o
| dGIsREM9Y29ycD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlm
| aWNhdGlvbkF1dGhvcml0eTBUBgNVHREBAf8ESjBIoCMGCisGAQQBgjcUAgOgFQwT
| QVVUSE9SSVRZJEBodGIuY29ycIISYXV0aG9yaXR5Lmh0Yi5jb3JwgghodGIuY29y
| cIIDSFRCMA0GCSqGSIb3DQEBCwUAA4IBAQCH8O6l8pRsA/pyKKsSSkie8ijDhCBo
| zoOuHiloC694xvs41w/Yvj9Z0oLiIkroSFPUPTDZOFqOLuFSDbnDNtKamzfbSfJR
| r4rj3F3r7S3wwK38ElkoD8RbqDiCHan+2bSf7olB1AdS+xhp9IZvBWZOlT0xXjr5
| ptIZERSRTRE8qyeX7+I4hpvGTBjhvdb5LOnG7spc7F7UHk79Z+C3BWG19tyS4fw7
| /9jm2pW0Maj1YEnX7frbYtYlO7iQ3KeDw1PSCMhMlipovbCpMJ1YOX9yeQgvvcg0
| E0r8uQuHmwNTgD5dUWuHtDv/oG7j63GuTNwEfZhtzR2rnN9Vf2IH9Zal
|_-----END CERTIFICATE-----
|_ssl-date: 2023-07-19T19:05:36+00:00; +4h00m00s from scanner time.
3269/tcp  open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: authority.htb, Site: Default-First-Site-Name)
| ssl-cert: Subject: 
| Subject Alternative Name: othername:<unsupported>, DNS:authority.htb.corp, DNS:htb.corp, DNS:HTB
| Issuer: commonName=htb-AUTHORITY-CA/domainComponent=htb
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2022-08-09T23:03:21
| Not valid after:  2024-08-09T23:13:21
| MD5:   d494 7710 6f6b 8100 e4e1 9cf2 aa40 dae1
| SHA-1: dded b994 b80c 83a9 db0b e7d3 5853 ff8e 54c6 2d0b
| -----BEGIN CERTIFICATE-----
| MIIFxjCCBK6gAwIBAgITPQAAAANt51hU5N024gAAAAAAAzANBgkqhkiG9w0BAQsF
| ADBGMRQwEgYKCZImiZPyLGQBGRYEY29ycDETMBEGCgmSJomT8ixkARkWA2h0YjEZ
| MBcGA1UEAxMQaHRiLUFVVEhPUklUWS1DQTAeFw0yMjA4MDkyMzAzMjFaFw0yNDA4
| MDkyMzEzMjFaMAAwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDVsJL0
| ae0n8L0Eg5BAHi8Tmzmbe+kIsXM6NZvAuqGgUsWNzsT4JNWsZqrRoHMr+kMC4kpX
| 4QuOHTe74iyB8TvucgvwxKEi9uZl6C5unv3WNFhZ9KoTOCno26adxqKPbzS5KQtk
| ZCvQfqQKOML0DuzA86kwh4uY0SjVR+biRj4IkkokWrPDWzzow0gCpO5HNcKPhSTl
| kAfdmdQRPjkXQq3h2QnfYAwOMGoGeCiA1whIo/dvFB6T9Kx4Vdcwi6Hkg4CwmbSF
| CHGbeNGtMGeWw/s24QWZ6Ju3J7uKFxDXoWBNLi4THL72d18jcb+i4jYlQQ9bxMfI
| zWQRur1QXvavmIM5AgMBAAGjggLxMIIC7TA9BgkrBgEEAYI3FQcEMDAuBiYrBgEE
| AYI3FQiEsb4Mh6XAaYK5iwiG1alHgZTHDoF+hKv0ccfMXgIBZAIBAjAyBgNVHSUE
| KzApBgcrBgEFAgMFBgorBgEEAYI3FAICBggrBgEFBQcDAQYIKwYBBQUHAwIwDgYD
| VR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCQYHKwYBBQIDBTAMBgorBgEE
| AYI3FAICMAoGCCsGAQUFBwMBMAoGCCsGAQUFBwMCMB0GA1UdDgQWBBTE4oKGc3Jv
| tctii3A/pyevpIBM/TAfBgNVHSMEGDAWgBQrzmT6FcxmkoQ8Un+iPuEpCYYPfTCB
| zQYDVR0fBIHFMIHCMIG/oIG8oIG5hoG2bGRhcDovLy9DTj1odGItQVVUSE9SSVRZ
| LUNBLENOPWF1dGhvcml0eSxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2Vydmlj
| ZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1odGIsREM9Y29ycD9j
| ZXJ0aWZpY2F0ZVJldm9jYXRpb25MaXN0P2Jhc2U/b2JqZWN0Q2xhc3M9Y1JMRGlz
| dHJpYnV0aW9uUG9pbnQwgb8GCCsGAQUFBwEBBIGyMIGvMIGsBggrBgEFBQcwAoaB
| n2xkYXA6Ly8vQ049aHRiLUFVVEhPUklUWS1DQSxDTj1BSUEsQ049UHVibGljJTIw
| S2V5JTIwU2VydmljZXMsQ049U2VydmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1o
| dGIsREM9Y29ycD9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlm
| aWNhdGlvbkF1dGhvcml0eTBUBgNVHREBAf8ESjBIoCMGCisGAQQBgjcUAgOgFQwT
| QVVUSE9SSVRZJEBodGIuY29ycIISYXV0aG9yaXR5Lmh0Yi5jb3JwgghodGIuY29y
| cIIDSFRCMA0GCSqGSIb3DQEBCwUAA4IBAQCH8O6l8pRsA/pyKKsSSkie8ijDhCBo
| zoOuHiloC694xvs41w/Yvj9Z0oLiIkroSFPUPTDZOFqOLuFSDbnDNtKamzfbSfJR
| r4rj3F3r7S3wwK38ElkoD8RbqDiCHan+2bSf7olB1AdS+xhp9IZvBWZOlT0xXjr5
| ptIZERSRTRE8qyeX7+I4hpvGTBjhvdb5LOnG7spc7F7UHk79Z+C3BWG19tyS4fw7
| /9jm2pW0Maj1YEnX7frbYtYlO7iQ3KeDw1PSCMhMlipovbCpMJ1YOX9yeQgvvcg0
| E0r8uQuHmwNTgD5dUWuHtDv/oG7j63GuTNwEfZhtzR2rnN9Vf2IH9Zal
|_-----END CERTIFICATE-----
|_ssl-date: 2023-07-19T19:05:36+00:00; +4h00m00s from scanner time.
5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
8443/tcp  open  ssl/https-alt syn-ack ttl 127
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 200 
|     Content-Type: text/html;charset=ISO-8859-1
|     Content-Length: 82
|     Date: Wed, 19 Jul 2023 19:04:37 GMT
|     Connection: close
|     <html><head><meta http-equiv="refresh" content="0;URL='/pwm'"/></head></html>
|   GetRequest: 
|     HTTP/1.1 200 
|     Content-Type: text/html;charset=ISO-8859-1
|     Content-Length: 82
|     Date: Wed, 19 Jul 2023 19:04:35 GMT
|     Connection: close
|     <html><head><meta http-equiv="refresh" content="0;URL='/pwm'"/></head></html>
|   HTTPOptions: 
|     HTTP/1.1 200 
|     Allow: GET, HEAD, POST, OPTIONS
|     Content-Length: 0
|     Date: Wed, 19 Jul 2023 19:04:35 GMT
|     Connection: close
|   RTSPRequest: 
|     HTTP/1.1 400 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 1936
|     Date: Wed, 19 Jul 2023 19:04:43 GMT
|     Connection: close
|     <!doctype html><html lang="en"><head><title>HTTP Status 400 
|     Request</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 400 
|_    Request</h1><hr class="line" /><p><b>Type</b> Exception Report</p><p><b>Message</b> Invalid character found in the HTTP protocol [RTSP&#47;1.00x0d0x0a0x0d0x0a...]</p><p><b>Description</b> The server cannot or will not process the request due to something that is perceived to be a client error (e.g., malformed request syntax, invalid
|_http-favicon: Unknown favicon MD5: F588322AAF157D82BB030AF1EFFD8CF9
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn't have a title (text/html;charset=ISO-8859-1).
| ssl-cert: Subject: commonName=172.16.2.118
| Issuer: commonName=172.16.2.118
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2023-07-17T16:33:22
| Not valid after:  2025-07-19T04:11:46
| MD5:   cea1 8f52 a52b ea12 b6b4 33ac dacd fb88
| SHA-1: 8b0c 775c 4406 3e12 ff4f 1450 70e3 99a1 88af 4e55
| -----BEGIN CERTIFICATE-----
| MIIC5jCCAc6gAwIBAgIGEmZU0nb6MA0GCSqGSIb3DQEBCwUAMBcxFTATBgNVBAMM
| DDE3Mi4xNi4yLjExODAeFw0yMzA3MTcxNjMzMjJaFw0yNTA3MTkwNDExNDZaMBcx
| FTATBgNVBAMMDDE3Mi4xNi4yLjExODCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
| AQoCggEBAJcDZlNGHGVmWnajGm9qKa33KpJpJHtePeSOTn5ikdO39ObuQQHBRMXb
| NdBysJaR2NRf7Ue+Xe+sYTp0NRIKo7832gR15vPaRGpA7z/XJ40dFWAplfllaEXw
| uIomh1qmCY7y12QN7U3B/+ToAD9QDqu4W+SFHYXxApAeXi+ADvRvcoc1Nqk/vXJT
| cg1wx+dR1t/DGwnc8RnxWQ7PNlEDD1CAKE7IoiYM6C/3dyXOBnTwYM3wlBDJqWlz
| TEefbVSYbqsxp4QVkE1R726weoiEdGClRVmy5I0RmhWQDeOyKw9LLiAgu3Yt1aDk
| HZpGvrPOdUOzg0mrl8CmgeHCIphePbkCAwEAAaM4MDYwDAYDVR0TAQH/BAIwADAO
| BgNVHQ8BAf8EBAMCBaAwFgYDVR0lAQH/BAwwCgYIKwYBBQUHAwEwDQYJKoZIhvcN
| AQELBQADggEBAAelPX3C2vx1r/sjaWzxJ7z/eWi1netyOex9QnweCXPo8v5f7bm3
| DkE24YsYOBUQ3IBDBnyMMRm85RXFbgaZq+kNxEuGPyhGVRVS7u8m97eVK4Z5wxwq
| s+w7t4QE+GvZQkQdlqTVv2ZWGCeD3on04BvO5Vjl9N616JHaRzgr3gJhhZ+oPwLt
| FNiS5ZxDBbuL6QJllYlXYG4DXZGiVyFaOyOXchfhX2WPA70iC9xymijpt26vFYv2
| CuQTMdyf+nheVmEX2suW7trKfbTjd3OY4YR7yKjD2Z5lamrgNAL8w3Cp686nyqMq
| BWF6iKL/bOj54LUGpLcBiASsdFTyZ4pA5jc=
|_-----END CERTIFICATE-----
9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49684/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
49685/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49687/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49688/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49697/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49707/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
49710/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
50784/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8443-TCP:V=7.91%T=SSL%I=7%D=7/19%Time=64B7FB83%P=x86_64-unknown-lin
SF:ux-gnu%r(GetRequest,DB,"HTTP/1\.1\x20200\x20\r\nContent-Type:\x20text/h
SF:tml;charset=ISO-8859-1\r\nContent-Length:\x2082\r\nDate:\x20Wed,\x2019\
SF:x20Jul\x202023\x2019:04:35\x20GMT\r\nConnection:\x20close\r\n\r\n\n\n\n
SF:\n\n<html><head><meta\x20http-equiv=\"refresh\"\x20content=\"0;URL='/pw
SF:m'\"/></head></html>")%r(HTTPOptions,7D,"HTTP/1\.1\x20200\x20\r\nAllow:
SF:\x20GET,\x20HEAD,\x20POST,\x20OPTIONS\r\nContent-Length:\x200\r\nDate:\
SF:x20Wed,\x2019\x20Jul\x202023\x2019:04:35\x20GMT\r\nConnection:\x20close
SF:\r\n\r\n")%r(FourOhFourRequest,DB,"HTTP/1\.1\x20200\x20\r\nContent-Type
SF::\x20text/html;charset=ISO-8859-1\r\nContent-Length:\x2082\r\nDate:\x20
SF:Wed,\x2019\x20Jul\x202023\x2019:04:37\x20GMT\r\nConnection:\x20close\r\
SF:n\r\n\n\n\n\n\n<html><head><meta\x20http-equiv=\"refresh\"\x20content=\
SF:"0;URL='/pwm'\"/></head></html>")%r(RTSPRequest,82C,"HTTP/1\.1\x20400\x
SF:20\r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Language:\x20
SF:en\r\nContent-Length:\x201936\r\nDate:\x20Wed,\x2019\x20Jul\x202023\x20
SF:19:04:43\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20html><html\
SF:x20lang=\"en\"><head><title>HTTP\x20Status\x20400\x20\xe2\x80\x93\x20Ba
SF:d\x20Request</title><style\x20type=\"text/css\">body\x20{font-family:Ta
SF:homa,Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white;backg
SF:round-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-size:16
SF:px;}\x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x20{colo
SF:r:black;}\x20\.line\x20{height:1px;background-color:#525D76;border:none
SF:;}</style></head><body><h1>HTTP\x20Status\x20400\x20\xe2\x80\x93\x20Bad
SF:\x20Request</h1><hr\x20class=\"line\"\x20/><p><b>Type</b>\x20Exception\
SF:x20Report</p><p><b>Message</b>\x20Invalid\x20character\x20found\x20in\x
SF:20the\x20HTTP\x20protocol\x20\[RTSP&#47;1\.00x0d0x0a0x0d0x0a\.\.\.\]</p
SF:><p><b>Description</b>\x20The\x20server\x20cannot\x20or\x20will\x20not\
SF:x20process\x20the\x20request\x20due\x20to\x20something\x20that\x20is\x2
SF:0perceived\x20to\x20be\x20a\x20client\x20error\x20\(e\.g\.,\x20malforme
SF:d\x20request\x20syntax,\x20invalid\x20");
Service Info: Host: AUTHORITY; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 4h00m00s, deviation: 0s, median: 3h59m59s
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 34798/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 64473/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 45856/udp): CLEAN (Timeout)
|   Check 4 (port 37545/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled and required
| smb2-time: 
|   date: 2023-07-19T19:05:30
|_  start_date: N/A
```

In the scan results I observed 2 domains runnig on the machine:

```
- htb.corp
- authority.htb.corp
```

I added both of them to my host config file.

### DNS enumeration:

As the port 53 is open & DNS server is running, I performed some DNS enumeartion activity.

Checked for the DNS details:

```bash
dig htb.corp any @10.10.11.222
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ffba9a9a-1624-4b30-a0a1-bf642f304029)

Checked for the nameserver & it again revealed the "authority.htb.corp"
```bash
dig htb.corp any @10.10.11.222 -t NS
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1804fb57-14d7-4197-b134-9d15487af72f)

```bash
dig axfr htb.corp any @10.10.11.222
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c129e868-3a24-4a36-9980-c45795421f2d)

So, DNS enumeration didn't revealed anything much intresting.

### Web enumeration:

When I didn't found much in the DNS, I moved onto checking web since port 80 & 443 is open.
I started the sub-domain & directory enumeration on both the domains but except Microsoft IIS web server I didn't found anything.

I then moved onto checking port 8443 & found that there is an application "PWM" running on the server.
As per the google search: "PWM is an open source password self-service application for LDAP directories." It's running on version "v2.0.3 bc96802e"

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5721689b-1632-409b-ad65-909447334abd)

### SMB enumeration

As the port 445 is open & running I checked for the shares where I have access as a guest user.
I observed that there is one share "Development" in which I have read access as a guest user.

```bash
smbmap -H "htb.corp" -s Development -u 'DoesNotExist'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bd418dfb-e193-44c9-b8bd-2c1d2035dd33)

This developement share has some automation & PWM configuration file present in it.

```bash
smbmap -H "htb.corp" -R "Development" -u 'DoesNotExist'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/051ce1f1-73fd-4eeb-9236-4eced8c1c46b)

I connected to this share & downloaded everything from it into my attacker machine.

```bash
smbclient \\\\htb.corp\\Development -U 'Guest'

smb: \> prompt off
smb: \> recurse on
smb: \> mget *
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/62465e43-1b8d-4a34-8982-0cf9625a46dd)

In one of the file 'ansible.cfg' I observed a remote user "svc_pwm".

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a690bc9a-c303-4336-b3bb-1abd19b92491)

In another file "ansible_inventory" I got some credentials for winrm associated with the user "Administrator"

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d9ca85e7-7ec1-4e8c-a4ca-cd597852123f)

I tried to login with these credentials but none of them worked, then I moved further & looked into another directory where I found some ANSIBLE hashes in this directory:

```
Automation/Ansible/PWM/defaults
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c67ebf59-7182-4ca7-87ec-f02238cc1e70)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

So, now I have the ANSIBLE hashes & we can crack these hashes using John the ripper & after cracking we can get use "ansible decrpyt" function to get the secret from the Ansible vault.

To convert the hashes into John the ripper format we can use:

```bash
ansible2john.py ansible.yml > ansi #we can do the same for other 2 hashes as well.
```

I created 3 files for 3 different hashes to convert & crack using John.

After converting the format we can crack it using below command:

```bash
john ansi -w=/usr/share/wordlists/rockyou.txt
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0ef666a1-c546-44d0-bd1f-5352b08e78ab)

once cracking the file & extracting the password from it we can use it to extract the secret from ANSIBLE files, as referred from this [blog](https://www.shellhacks.com/ansible-vault-encrypt-decrypt-string/):

```bash
cat ansible1.yml | ansible-vault decrypt
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f56ad7b8-093f-4f28-a7df-45d83eff6d30)

While logging in, I got some error when I entered wrong credentials where there is another host relvealed running inside the Authority box, which I added to my hosts file.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dc35f556-f9a7-4f41-8a7d-c883caf57080)

Moving further I then moved on configuration manager page, where I successfully logged in from one of the extracted credentials from Ansible vault. 🙂

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c54e38a2-6134-49b7-8810-f635a06d0667)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## User access:

When I logged into the configuration manager I was looking around the functionalities & found some intresting options in the LDAP directory connection settings. I observed that there are some saved credentials in the LDAP proxy vault.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/37f5490c-1d4b-430c-bc3b-6e9d482edba8)

Also, there is an option of testing LDAP url. This scenario reminded me of [LDAP passback attack](https://medium.com/r3d-buck3t/pwning-printers-with-ldap-pass-back-attack-a0d8fa495210), which I learned almost an year ago.

So, I turned on the rouge LDAP server on my host which I already setup last year using OpenLDAP & also started tcpdump to listen all incoming connections on port 389:

```bash
sudo tcpdump -SX -i tun0 tcp port 389
```
And added my IP into the test LDAP url section & clicked on "Test ldap profile". Quickly after clicking I started receiving the packets & in on of the packet I observed a user "svc_ldap" & clear text credentials with it:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/afb75451-1e2d-4c1e-b379-c655ed9aca65)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dcb93f11-f029-409f-b7fa-99fbea49b478)

Using these credentials I tried to login via win-rm protocol & I logged in successfully. Also, after logging in with "svc_ldap" I got user flag as well. (pwn3d!🙂)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

Starting for the privilege escalation when I enumerated through the directories I observed a directory in "C:\" called :"certs"

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9fa0fe59-431b-44a5-b65d-2f497d7b90c5)

This directory & the box name "Authority" hinted towards the certifcate authority templates, where I can use vulnerbale certificate templates to escalate my privilege or to impersonate any user.

I used a binary "[certify.exe](https://github.com/GhostPack/Certify)" to check for all the available vulnerable certificates.
Searching for the vulnerable certifiactes using "Certify" showed 1 available certificate "Corpvpn", which we can request.

```PS
.\Certify.exe find /vulnerable
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d27cfafb-6331-49b0-8048-fe78f0de4baf)

From the [hactrickz](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/ad-certificates/domain-escalation#misconfigured-certificate-templates-esc1) website I got to know that, the "ENROLLEE_SUPPLIES_SUBJECT" flag allows the certificate requester to define the subject details, which are typically used for identification purposes, such as the common name, organization, and other attributes.

This indicates we can add a new computer account in Active Directory using the credentials of a domain user. In this case, I will use the "[impacket-addcomputer](https://tools.thehacker.recipes/impacket/examples/addcomputer.py)" tool.

```bash
python3 /opt/impacket/examples/addcomputer.py authority.htb.corp/svc_ldap:'lDaP_1n_th3_cle4r!' -computer-name 'FXI$' -computer-pass 'Passw0rd1!'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/3108b696-b9f5-4d3a-b9ef-2f207ff9f141)

Now using Certipy, we can generated a certificate request using the computer account "FXI$" with the password "Passw0rd1!" and the template "CorpVPN," which will allow us to escalate privileges.





