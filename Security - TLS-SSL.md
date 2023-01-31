#security/tls

---

[Transport Layer Security](https://www.techtarget.com/searchsecurity/definition/Transport-Layer-Security-TLS)
[Local Copy](zDOC_security_tls.mhtml)

[An Introduction to Cipher Suites](https://www.keyfactor.com/blog/cipher-suites-explained/)  
[Local Copy](app://obsidian.md/zDOC_security_intro_to_chipher_suites.mhtml)
- Registry
Get list of chipheas available on a server
```
reg query "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Cryptography\Configuration\Local\SSL\0001000**2**" /v Functions
```
- IISCrypto tool

https://ciphersuite.info/cs/

IANA, OpenSSL and GnuTLS use different naming for the same ciphers.
[Cipher suite correspondence table](https://wiki.mozilla.org/Security/Cipher_Suites)

