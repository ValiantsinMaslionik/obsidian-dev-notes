#security/ssl

---

[SSL/TLS connection issue troubleshooting test tools](https://techcommunity.microsoft.com/t5/azure-paas-blog/ssl-tls-connection-issue-troubleshooting-test-tools/ba-p/2240059)
[Local Copy](zDOC_security_troubleshooting_ssl_tls_connection_issue.mhtml)

Could you please list ciphers available in system via command:  
```
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Cryptography\Configuration\Local\SSL\00010002" /v Functions
```


# curl

https://curl.se/windows/
https://curl.se/docs/manual.html


```ps1
curl.exe -v -x http://webproxy.e.corp.services:80 -L https://rendition-retrieval-qa.ras.int.thomsonreuters.com
curl.exe -v -x http://webproxy.e.corp.services:80 -L https://rendition-retrieval-qa.ras.int.thomsonreuters.com --tlsv1.0
curl.exe -v -x http://webproxy.e.corp.services:80 -L https://rendition-retrieval-qa.ras.int.thomsonreuters.com --tlsv1.2
```