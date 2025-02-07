Yes, you can inject a blob URL into an iframe to display a different website that occupies the entire viewport.  However, "injecting" implies that you're somehow adding the iframe to a page you don't control, which raises security concerns and is generally unethical if done without the website owner's permission.

I'll show you how to create the scenario you've described, but I want to emphasize that this should only be used on websites you own or have explicit permission to modify.  Attempting to inject iframes into websites without permission is illegal and harmful.

Here are two main approaches, along with important security considerations:

**1. If you control the parent page:**

This is the legitimate and safe way to do it.  You simply add the iframe to your HTML and set its `src` to the blob URL.

```html
<!DOCTYPE html>
<html>
<head>
<title>Iframe with Blob URL (Full Viewport)</title>
<style>
  body, html { margin: 0; padding: 0; width: 100%; height: 100%; overflow: hidden; }
  iframe { width: 100%; height: 100%; border: none; }
</style>
</head>
<body>

<iframe id="myIframe"></iframe>

<script>
  const websiteContent = `
    <!DOCTYPE html>
    <html>
    <head>
      <title>Blob Content</title>
      <style> body, html { margin: 0; padding: 0; width: 100%; height: 100%; } body { background-color: lightblue; } </style>
    </head>
    <body> <h1>Content from Blob URL</h1> </body>
    </html>
  `;

  const blob = new Blob([websiteContent], { type: 'text/html' });
  const blobUrl = URL.createObjectURL(blob);

  const iframe = document.getElementById('myIframe');
  iframe.src = blobUrl;

  // Optional: Revoke the blob URL after a delay
  // setTimeout(() => URL.revokeObjectURL(blobUrl), 30000);
</script>

</body>
</html>
```

**2.  If you *don't* control the parent page (and are doing this for testing purposes on a site you own or with permission):**

This is where it gets more complicated and potentially problematic.  You would need some way to execute JavaScript on the target page.  This could be through a browser extension, a bookmarklet, or (in very rare cases) a vulnerability in the website itself (which you should report responsibly, not exploit).

**Example using a bookmarklet (for testing purposes only):**

1.  Create a bookmark in your browser.
2.  Edit the bookmark's URL and paste in the following JavaScript code (replace the placeholder `websiteContent` with your desired content):

```javascript
javascript:(function() {
  const websiteContent = `<!DOCTYPE html><html><head><title>Blob Content</title><style>body, html { margin: 0; padding: 0; width: 100%; height: 100%; } body { background-color: lightblue; }</style></head><body><h1>Content from Blob URL</h1></body></html>`;
  const blob = new Blob([websiteContent], { type: 'text/html' });
  const blobUrl = URL.createObjectURL(blob);

  const iframe = document.createElement('iframe');
  iframe.style.width = '100%';
  iframe.style.height = '100%';
  iframe.style.border = 'none';
  iframe.src = blobUrl;

  document.body.innerHTML = ''; // Clear existing content (use with caution!)
  document.body.appendChild(iframe);


  // Optional: Revoke URL after delay
  // setTimeout(() => URL.revokeObjectURL(blobUrl), 30000);

})();
```

3.  When you are on the target website (that you own or have permission to test on!), click the bookmark.  This will execute the JavaScript, injecting the iframe with the blob URL.

**Extremely Important Security Considerations:**

* **Cross-Origin Resource Sharing (CORS):**  Browsers have security mechanisms (CORS) that prevent JavaScript on one domain from accessing resources on a different domain *unless* the server on the other domain explicitly allows it.  If you are trying to inject an iframe into a website that you do not control, CORS is very likely to prevent your injected JavaScript from being able to modify the parent page in any meaningful way.
* **Same-Origin Policy:**  The Same-Origin Policy is another critical security feature that restricts how documents or scripts loaded from one origin can interact with resources from a different origin.  This policy would limit your ability to access or manipulate elements in the parent page from your injected iframe if the origins are different.
* **Ethical Considerations:**  Injecting iframes into websites without the owner's consent is unethical and can be illegal.  Do not attempt to use these techniques on websites you do not own or have permission to modify.  The bookmarklet example is provided purely for testing and educational purposes on sites you control.

**In summary:** Injecting iframes into pages you don't control is generally blocked by browser security features (CORS, Same-Origin Policy) and is unethical and potentially illegal.  The proper and safe way to use iframes with blob URLs is to add the iframe directly to the HTML of a page you own.
