# Universal Links

Two step process:

1. Entitlement in the app
2. File on the web

> To associate a website with an app, you will need to have a file on your website
> and an entitlement in your app.

> An associated domain matches the Associated Domains Entitlement in your app with
> an apple-app-site-association file on your website.

## Apple App Site Association File

Wildcard subdomains shouldn't be a problem. From [the docs](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html)

> To match all subdomains of an associated domain, you can specify a wildcard by
> prefixing *. before the beginning of a specific domain (the period is required).
> Domain matching is based on the longest substring in the applinks entries.
> For example, if you specify the entries applinks:*.mywebsite.com and
> applinks:*.users.mywebsite.com, matching for the domain emily.users.mywebsite.com
> is performed against the longer *.users.mywebsite.com entry. Note that an entry
> for *.mywebsite.com does not match mywebsite.com because of the period after
> the asterisk. To enable matching for both *.mywebsite.com and mywebsite.com,
> you need to provide a separate applinks entry for each.

So every subdomain must serve up the `apple-app-site-association` json file.

### Instructions for mobile

1. Enable _Associated Domains_ entitlement in Capabilities tab
2. Click `+` to add an associated domain
3. Remove the `webcredentials:` placeholder
4. Add an entry for `applinks:*.instructure.com` and `applinks:*.canvaslms.instructure.com`

### Instructions for canvas web

Every account should serve up a `apple-app-site-association` file with the
following json at `https://<account>.instructure.com/.well-known/apple-app-site-association`
and `https://<account>.canvaslms.instructure.com/.well-known/apple-app-site-association`

```
{
    "applinks": {
        "apps": [],
        "details": [{
            "appID": "8MKNFMCD9M.com.instructure.icanvas",
            "paths": ["*"]
            },
            {
            "appID": "8MKNFMCD9M.com.instructure.ios.teacher",
            "paths": ["*"]
        }]
    },

    // Optional if we want webcredentials
    "webcredentials": {
       "apps": ["8MKNFMCD9M.com.instructure.icanvas", "8MKNFMCD9M.com.instructure.ios.teacher"]
    }
}
```

## Shared web credentials
This is a way for the native app to access passwords stored in Keychain that were
written to from web.

Doesn't seem like a good fit for us.

## Resources
* [Enabling Universal Links](https://developer.apple.com/documentation/uikit/core_app/allowing_apps_and_websites_to_link_to_your_content/enabling_universal_links)
* [Setting up Associated Domains](https://developer.apple.com/documentation/security/password_autofill/setting_up_an_app_s_associated_domains)
* [Associated Domains Entitlement](https://developer.apple.com/documentation/bundleresources/entitlements/com_apple_developer_associated-domains)
* [Support Universal Links](https://developer.apple.com/library/archive/documentation/General/Conceptual/AppSearch/UniversalLinks.html)
* [Web Credentials tutorial](https://medium.com/tech-tajawal/seamless-login-experience-with-password-autofill-and-shared-web-credentials-6a232fa5f54d)
