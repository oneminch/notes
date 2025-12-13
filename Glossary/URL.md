- A URL, which stands for Uniform Resource Locator, is a string of text that defines the location of a resource on the web. Paths are used to specify where certain files are located in the filesystem.
- An ==absolute URL== points to an absolute location on the web where its definition contains the web protocol and domain. It always points to the same location, wherever it's used.
- A ==relative URL== points to a resource relative to the location where it is linked from. It will point to different locations depending on where it's linked from.
- URL schemes can be used to perform specific purposes:
    - **`sms:<phone-number>`**
    - **`tel:<phone-number>`**
    - **`mailto:<optional-email-address>`** - opens an email client with new outgoing email message either to an email address (if an email address is specified) or to empty address (if email address is omitted). The latter case is often used as a "Share with email" option to send to an address of the users choosing.

> [!info]
> With `mailto` protocol, more details of the email can be provided in the URL using standard URL query notation. Values of each field must be URL-encoded; non-printing characters (like tabs, carriage returns, page breaks, and spaces) must be percent-encoded.

```html
<a href="tel:1-800-000-0000">Call us: +1(800)000-0000</a>

<a href="sms:1-800-000-0000">Text us: +1(800)000-0000</a>
<a href="sms:1-800-000-0000,1-800-000-1000?body=hello%20there">
	Text us: +1(800)000-0000
</a>

<a href="mailto:hi@email.com">Send us an email!</a>
<a
	href="mailto:hi@email.com?cc=sales@email.com&bcc=privacy@email.com&subject=The%20subject%20of%20the%20email&body=The%20body%20of%20the%20email"
>
	Send an email with cc, bcc, subject and body
</a>
```
