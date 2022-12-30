+++
title = "Setting headers in Vite"
date = 2022-12-30
draft = false

[taxonomies]
categories = ["Features"]
tags = ["js", "vue"]
+++

This post covers how to turn your [Mozilla Observatory](https://observatory.mozilla.org/) scores from **D-** to **A+** on your Vite website.

<!-- more -->
Mozilla Observatory measures the security of your website using a range of factors to determine how safe it is for your users to interact with. With an age of ever increasing hacking exploits, it is becoming more important to create safer websites.

It recommends the creation if more headers to secure your website:
* [Content Security Policy](https://infosec.mozilla.org/guidelines/web_security#content-security-policy)
* [HSTS](https://infosec.mozilla.org/guidelines/web_security#http-strict-transport-security)
* X "options": 
  * [X-Content](https://infosec.mozilla.org/guidelines/web_security#x-content-type-options)
  * [X-Frame](https://infosec.mozilla.org/guidelines/web_security#x-frame-options)
  * [X-XSS](https://infosec.mozilla.org/guidelines/web_security#x-frame-options)

Out of these three options, X options are these easiest to implement (and least likely to break your site).

In your `vite.config.js` you can adjust your header settings:

```js
// FILE: vite.config.js

import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
    server: {
        headers: {
            'Strict-Transport-Security': 'max-age=86400; includeSubDomains', // Adds HSTS options to your website, with a expiry time of 1 day
            'X-Content-Type-Options': 'nosniff', // Protects from improper scripts runnings
            'X-Frame-Options': 'DENY', // Stops your site being used as an iframe
            'X-XSS-Protection': '1; mode=block' // Gives XSS protection to legacy browsers
        }
    }
})
```

<div class="box error">
    <i type="Button" class="svg error-icon" title="Warning Icon"></i>
    <p>
    Make sure to test all header attributes as they can severely break websites if you are not careful.
    </p>

</div>

## CSP header
This header has personally caused me the strife (I left it out of the base config).

This header restricts what files can load into your website.

If you want to add it I recommend reading through the [documentation](https://infosec.mozilla.org/guidelines/web_security#content-security-policy).
```js
        // --snip--
        headers: {
            'Content-Security-Policy': 'upgrade-insecure-requests',
            // --snip--
        }
```
This policy does not break anything on your website, but other policies like `default-src 'self'` can easily break your website. So, just test what works.

### Unsafe-inline
Some CSP policies (`script-src` and `style-src`) prevent the inline loading of resources, which a vue file does natively. So use `unsafe-inline` to try and get the policy to work. 

```js
        // --snip--
        headers: {
            'Content-Security-Policy': 'script-src: unsafe-inline; style-src: unsafe-inline',
            // --snip--
        }
```



