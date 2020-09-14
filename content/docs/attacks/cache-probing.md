+++
title = "Cache Probing"
description = ""
category = "attacks"
attacks = [
    "dom property",
]
defenses = [
    "same-site cookies",
    "sec-fetch metadata",
]
menu = "main"
+++

The Cache Probing principle consists in detecting whether some resource was cached by the browser. The concept is known since the begging of the web [^4] and initially used timing differences to succeed. 

When a user visits a website, some resources such as images, scripts, and HTML content are fetched and later cached by the browser (under certain conditions). This optimization will make next to the navigations faster, as the browser will serve those resources from disk memory and avoid possible high latency requests. So, when a user visits a website, some images and resources will eventually be stored in the cache. If an attacker can detect if one of those resources is stored in the cache, it can be enough to leak whether the user accessed that website in the past. 

A variation to the original concept was published and abused [Error Events](https://TODO-REFFERSUBSECTIONBELLOW) to perform more accurate and impactful attacks, including tricks to purge resources from the cache (to make better inferences).

### Attack Principle

An attacker wants to know whether a user visited a certain social network.

1. A user visits a social network and some of the subresources will be cached. This step is not required.
2. The user visits an attacker-controlled page which will fetch a resource that is usually fetched by the social network. 
3. Using a [Network Timing XS-Leak](https://TODO), the attacker page can infer the difference from a request coming from the cache (step 1 happened) or coming from a network request based on the request timing (step 1 did not happen). The delay will be significantly lower in a request served from the cache.

### Cache Probing with Error Events




## Defense

### Opt-in Defense Mechanisms

| [Same-Site Cookies](https://TODO)   | [Vary: Sec-Fetch-Site]({{< ref "../defenses/opt-in/fetch-metadata.md#fetch-metadata--cache-probing-attacks" >}})  | [Subresource Protections]({{< ref "../defenses/design-protections/subresource-protections.md" >}}) |
|:---------------------------------:|:-------------------------------------:|:---------------------------------------:|
|        ✔️ (if strict)             |                  ✔️                   |   ❓[*](https://TODO-referdeploysectioninsubresourceprotection)   | 

- If a resource can be fetched only with user authentication, [Same-Site Cookies](https://TODO) (Strict) can be considered to protect that resource from being abused.
- [Vary: Sec-Fetch-Site]({{< ref "../defenses/opt-in/fetch-metadata.md#fetch-metadata--cache-probing-attacks" >}}) allows applications to force cache segregation by a group of origins. It does not enforce full segregation, since some resources come from Content delivery networks and used by multiple websites.
- [Subresource Protections]({{< ref "../defenses/design-protections/subresource-protections.md" >}}) allow application to set random tokens in URLs to make them unpredictable to attackers. Useful for both authenticated and unauthenticated subresources.

### No-opt Defense Mechanisms

[Paritioned Caches](https://TODO), a no-opt feature implemented in browsers can help mitigate this attack by creating a unique cache for each origin. Application do not have to opt-in to enforce this cache.

{{< hint warning >}}
Partitioned Caches are not available in most browsers by default, so applications cannot rely on them yet.
{{< /hint >}}

## Real World Example

1. An attacker using the [Error Events Cache Probing](https://TODO) could detect wether a user watched a specific YouTube Video by checking if the video thumbnail ended up in browser cache [^3].

## References

[^1]: Abusing HTTP Status Codes to Expose Private Information, [link](https://www.grepular.com/Abusing_HTTP_Status_Codes_to_Expose_Private_Information)
[^2]: HTTP Cache Cross-Site Leaks, [link](http://sirdarckcat.blogspot.com/2019/03/http-cache-cross-site-leaks.html)
[^3]: Mass XS-Search using Cache Attack, [link](https://terjanq.github.io/Bug-Bounty/Google/cache-attack-06jd2d2mz2r0/index.html#VIII-YouTube-watching-history)
[^4]: Timing Attacks on Web Privacy, [link](http://www.cs.jhu.edu/~fabian/courses/CS600.424/course_papers/webtiming.pdf)