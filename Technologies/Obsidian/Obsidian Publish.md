# Obsidian Publish
We don't yet have a way to provision SSL certificate on your behalf. If you want to enable HTTPS for a custom domain, you either need to use your own proxy or create a site on CloudFlare.

## Set up using CloudFlare

The easiest way to set up a custom domain or subdomain is to create a [CloudFlare](https://cloudflare.com/) account and let CloudFlare manage your domain's DNS.

When you migrate to CloudFlare, you automatically keep all your existing DNS setup, including your subdomains, email service, and hosting services. Using CloudFlare can speed up your websites and add SSL to your sites for free.

Typically, users host their Obsidian Publish content on a root domain (`mysite.com`) or an immediate subdomain (`notes.mysite.com`).

**CloudFlare:**

1.  Open Cloudflare to the domain you wish to add Publish to, such as `mysite.com`, even if you want a subdomain like `notes.mysite.com`.
2.  Go to **DNS** and click **Add Record**.
3.  Select **CNAME**
4.  In **name**, enter your domain or subdomain, for example `notes.mysite.com`.
5.  In **target**, enter `publish-main.obsidian.md`. Don't include your personal sub-URL in this value. Obsidian Publish handles this from your configuration.
6.  Go to **SSL/TLS** and set the SSL/TLS encryption mode to "Full" to configure the SSL/TLS certificate automatically.

**Note:** To redirect both `mysite.com` and `www.mysite.com` to Obsidian Publish, you need to create a [Page Rule](https://support.cloudflare.com/hc/en-us/articles/200172336-Creating-Page-Rules) with the following settings:

-   URL match: `www.mysite.com/*`
-   Forward URL - 301 Permanent Redirect
-   Redirect URL: `https://mysite.com/$1`

Once you create the page rule, you should also create a CNAME record for `www.mysite.com` just like you created for `mysite.com`

**Obsidian:**

1.  In ribbon, to the left of the application window, click **Publish changes** (paper plane icon).
2.  In the **Publish changes** dialog, click **Change site options** (cog icon).
3.  Next to **Custom domain**, click **Configure**.
4.  In **Custom URL**, enter the URL to your domain or subdomain.

**Note:**: If your custom domain setup ends up in a redirect loop, it's likely that the encryption mode in CloudFlare has been set to "Flexible" instead of "Full".