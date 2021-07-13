+++
title = "CSS Tricks"
description = ""
date = "2020-10-01"
category = [
    "Attack"
]
abuse = [
    "CSS"
]
defenses = [
    "Framing Protections"
]
menu = "main"
weight = 2
+++

## CSS Tricks
CSS can be used to trick a user into exposing information such as embedded pixel values by making visual changes that are affected by the embed.

## Retrieving user's history
Using the CSS [`:visited`](https://developer.mozilla.org/en-US/docs/Web/CSS/:visited) selector, it’s possible to apply a different style for URLs that have been visited.  
Previously it was possible to use [`getComputedStyle()`](https://developer.mozilla.org/en-US/docs/Web/API/Window/getComputedStyle) to detect this difference but now browsers prevent this by always returing values as if the link was visted and limiting what styles can be applyed using the selector. [^changes-1]   
So, it may be needed to trick the user into clicking an area that the CSS has affected this can be done using [`mix-blend-mode`](https://developer.mozilla.org/en-US/docs/Web/CSS/mix-blend-mode). [^blend-mode]   
There are also ways to do it without user interaction such as by abusing render timings this works because it takes time to paint links a different color. [^render-timings]  
A PoC was provided on a chromium report that works by using multiple links to increase the time difference. [^render-timings-bug]
{{< hint info >}} [^leak-1] shows an example of this attack using a whack a mole game to trick the user into clicking areas of the page, multiple bugs were reported about this issue: [^bug-1](https://bugs.chromium.org/p/chromium/issues/detail?id=712246), [^bug-2](https://bugs.chromium.org/p/chromium/issues/detail?id=713521), [^bug-3](https://bugzilla.mozilla.org/show_bug.cgi?id=147777){{< /hint >}}

## Evil Captcha
Using CSS, it’s possible to take an embed out of context.  
An example of this is pretending it’s a captcha as seen in [^leak-2]  
This works by setting the width and hight of an embed so that only the target characters are shown,
this may use multiple embeds to change the order of the characters being displayed so that its harder for a user to know what infomation there providing.

## Abusing autocomplete
If a website uses text inputs and does not opt-out of [autocomplete](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/autocomplete) using ```autocomplete="off"``` it may be possible to leak data such as email addresses. By tricking the user into pressing the keys to navigate the autocomplete ui for a javascript focused text input.
For chrome this requires the user to be tricked into pressing the Up or Down arrow key which opens the dialog and selects a value then by pressing Enter or Tab the value gets inserted into the page.
```javascript
let input = document.createElement("input");
input.type = "email";
input.autocomplete = "email";
input.name = "email";
input.size = "1";
input.style = "position:absolute;right:-500px;bottom:-21.9px";
input.onkeypress = e => {
    e.preventDefault();
}
window.onmousedown = e => {
    // ignore mouse clicks
    e.preventDefault();
}
input.onchange = e => {
    alert(e.srcElement.value);
    e.srcElement.value = "";
}
document.body.appendChild(input);
setInterval(() => {
    input.focus({preventScroll: true});
}, 1000);
```

## Custom cursor
Might not leak data directly but it may help trick the user,
as a large cursor may overlay the autocomplete dialog and other native ui.
```html
<style>
:root {
  cursor: url('https://www.google.com/favicon.ico'), auto;
}
</style>
```

## Defense
XFO prevents embeds from being attacked because theres no visual difference as the content does not get shown,
The *Retrieving user's history* attack can be only prevented by the user. It can be done by disabling the browser history, or if on Firefox, by setting the option `layout.css.visited_links_enabled` to `false` in `about:config` panel.  
| [SameSite Cookies (Lax)]({{< ref "/docs/defenses/opt-in/same-site-cookies.md" >}}) | [COOP]({{< ref "/docs/defenses/opt-in/coop.md" >}}) | [Framing Protections]({{< ref "/docs/defenses/opt-in/xfo.md" >}}) |                  [Isolation Policies]({{< ref "/docs/defenses/isolation-policies" >}})                   |
| :--------------------------------------------------------------------------------: | :-------------------------------------------------: | :---------------------------------------------------------------: | :------------------------------------------------------------------------------------------------------: |
|                                         ❌                                          |                          ❌                          |                                 ✔️                                 | ❌  |
## References
[^leak-1]: Whack a mole game, [link](https://lcamtuf.coredump.cx/whack/)  
[^changes-1]: Privacy and the :visited selector, [link](https://developer.mozilla.org/en-US/docs/Web/CSS/Privacy_and_the_:visited_selector)  
[^blend-mode]: CSS mix-blend-mode is bad for your browsing history, [link](https://lcamtuf.blogspot.com/2016/08/css-mix-blend-mode-is-bad-for-keeping.html)  
[^render-timings]: Pixel Perfect Timing Attacks with HTML5, [link](https://go.contextis.com/rs/140-OCV-459/images/Pixel_Perfect_Timing_Attacks_with_HTML5_Whitepaper%20%281%29.pdf)  
[^render-timings-bug]: Visited links can be detected via redraw timing, [link](https://bugs.chromium.org/p/chromium/issues/detail?id=252165)
[^leak-2]: The Human Side Channel, [link](https://ronmasas.com/posts/the-human-side-channel)  
