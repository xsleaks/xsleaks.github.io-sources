+++
title = "Compression leaks"
description = ""
date = "2020-10-08"
category = [
    "Attack",
]
abuse = [
    "Window References",
]
defences = [
    "Cross-Origin-Opener-Policy",
    "SameSite Cookies"
]
menu = "main"
weight = 2
+++
When compression is used and there’s reflected input such as from the URL or chat messages it may be possible to leak the contents of a website.
This is because when content is repeated it is more compressible which results in a smaller download that is detectable using timing attacks such as using the window "unload" event.
The benefit of this is that the size change is controlled by the attacker so the server can always respond with same response.

## HIST (2016)
An example of this attack is [HIST](https://www.blackhat.com/docs/us-16/materials/us-16-VanGoethem-HEIST-HTTP-Encrypted-Information-Can-Be-Stolen-Through-TCP-Windows.pdf)
this used timing attacks so that no MITM is needed,  
The primary defence for this attack is to disable third-party cookies however window navigation timings are not affected by this.
So also setting a Cross-Origin-Opener-Policy may make this attack harder since it prevents a window from being reused after a navigation away has been completed.

## BREACH (2013)
An example of this attack for leaking HTTPS traffic is [BREACH](http://breachattack.com/resources/BREACH%20-%20SSL,%20gone%20in%2030%20seconds.pdf) this uses a network sniffer to get the response size.

## TIME (2013)
Similar to HIST, [TIME](https://owasp.org/www-pdf-archive/A_Perfect_CRIME_TIME_Will_Tell_-_Tal_Beery.pdf) used timings attacks to detect the size differences.

## CRIME (2012)
Migrated for SPDY and TLS-level compression.

## Projects
There are multiple tools to exploit this behaviour such as [decrypto-org/rupture](https://github.com/decrypto-org/rupture) and
[nealharris/BREACH](https://github.com/nealharris/BREACH)

## Defences
- Disable compression
- Using separate requests for secrets prevents an attacker from injecting data.
- SameSite cookies prevents cookies being sent in cross origin elements.
- Cross-Origin-Opener-Policy prevents window reuse for timing attacks.
