# Informatin-Security-with-HelmetJS
A part of Information Security an Quality Assurance

## Introduction to Information Security with HelmetJs 
  HelmetJs is a type of middleware for Express-based applications that automatically sets HTTP headers to prevent sensitive information from unintentially being passed between the server and client. While HelmetJS does not account for all situations, it does include support for common ones like Content Security Policy, XSS Filter, and HTTP Strict Transport Security, among others. HelmetJS can be installed on an Express project from npm, after which each layer of protection can be configured to best fit the project.
  
### Install and Require Helmet.
  Helmet helps you secure your Express apps by setting various HTTP headers. Install the package, then require it.
  First step: npm install helmet npm install helmet
```python
var helmet = require("helmet");
```
### Hide Potentially Dangerous Information Using helmet.hidePoweredBy()
  Hackers can exploit known vulnerabilities in Express/Node if they see that your site is powered by Express. X-Powered-By: Express is sent in every request coming from Express by default. The helmet.hidePoweredBy() middleware will remove the X-Powered-By header. You can also explicitly set the header to something else, to throw people of
```python
  app.use(helmet.hidePoweredBy());
  app.use(helmet.hidePoweredBy({setTo: "PHP 4.2.0});
```
### Mitigate the Risk of Clickjacking with helmet.frameguard()

  Your page could be put in a <frame> or <iframe> without your consent. This can result in clickjacking attacks, among other things. Clickjacking is a technique of tricking a user into interacting with a page different from what the user thinks it is. This can be obtained executing your page in a malicious context, by mean of iframing. In that context a hacker can put a hidden layer over your page. Hidden buttons can be used to run bad scripts. This middleware sets the X-Frame-Options headers. It restricts who can put your sit in a frame. It has three modes: DENY, SAMEORIGIN, and ALLOW-FROM.
```python
  app.use(helmet.frameaguard({action: 'deny'});
```
  ### Mitigate the Risk of Cross site Scripting (XSS) Attacks with helmet.xssFilter();
   
  Cross-site scripting (XSS) is a frequent type of attack where malicious scripts are injected into vulnerable pages, with the purpose of stealing sensitive data like session cookies, or passwords.
  The basic rule to lower the risk of an XSS attack is simple: "Never trust user's input". As a developer you should always sanitize all the input coming from the outside. This inclues data coming from forms, GET query urls, and even from POST bodies. Sanitizing means that you should find and encode the characters that may be dangerous e.g <,>.
  Modern browsers can help mitigating the risk by adopting better software strategies. Often these are configurable via http headers.
  The X-XSS-Protection HTTP header is a basic protection. The browser detects a potential injected script using a heuristic filter. If the header is enable, the browser changes the script code, neutralizing it.
  ```python
  app.use(helmet.xssFilter());
  ```
  ### Avoid Inferring the Response MIME Type with helmet.noSniff()
  Browsers can use content or MIME sniffing to adapt to different datatypes coming from a response. They override the Content - Type headers to guess and process the data. While this can be convenient in some scenarios, it can also lead to some dangerours attacks. This middleware sets the X-Content-Type-Options header to nosniff. This instructs the browser to not bypass the provided Content-Type.
```python
  app.use(helmet.noSniff());
```
### Prevent IE from Opening Untrusted HTML with helmet.ieNoOpen()
  Some web applications will serve untrusted HTML for download. Some versions of Internet Explorer by default open those HTML files in the context of your site. This means that an unstrusted HTML page could start doing bad things in the context of your pages. This middleware sets the X-Download-Options header to noopen. This will prevent IE users from executing downloads in the trusted site's context.
```python
app.use(helmet.ieNoOpen());
```
### Ask Browsers to Access Your Site via HTTPS Only with helmet.hsts()
  HTTP Strict Transport Security (HSTS) is a web security policy which helps to project websites against protocol downgrade attacks and cookie hijacking. If your website can be accessed via HTTPS you can ask uer's browsers to avoid using insecure HTTP. By setting the header Strict-Transport-Security, you tell the browsers to use HTTPS for the future requests in a specified amount of time. This will work for the requests coming after the initial request.
  Configure helmet.hsts() to use HTTPS for the next 90 days. Pass the config object {maxAge: timeInMiliseconds, force: true}. Glitch already has hsts enabled. To override its settings you need to set the field "force" to true in the config object. We will intercept and restore the Glitch header, after inspecting it for testing.
```python
  var ninetyDaysInMilliseconds = 90*24*60*60*1000;
  app.use(helmet.hsts({maxAge: ninetyDaysInMilliseconds, force: true}));
  ```
  ### Disable DNS Prefetching with helmet.dsnPerfetchControl()
  To improve performance, most browsers prefetch DNS records for the links in a page. in that way the destination ip is already known when the user clicks on a link. this may lead to over-use of the DNS service (if you own a big website, visited by millions people...), privacy issues (one eavesdropper could infer that you are on a certain page), or page statistics alteration (some links may appear visited even if they are not). If you have high security needs you can disable DNS prefetching, at the cost of a performance penalty.
  ```python
  app.use(helmet.dnsPrefetchControl());
  ```
  ### Disable Client - Side Caching with helmet.noCache() -> have to download newver version -> lose benefits from cach
  If you are releasing an update for your website, and you want the users to always download the newer version, you can (try to) disable caching on client's browser. It can be useful in development too. Caching has performance benefits, which you will lose, so only use this option when there is a real need.
  ```python
  app.use(helmet.noCache());
  ```
  ### Set a Content Security Policy with helmet.contentSecurityPolicy()
  This challenge highlights one promising new defense that can significantly reduce the risk and impact of many type of attacks in modern browsers. By setting and configuring a Content Security Policy you can prevent the injection of anything unintended into your page. This will protect you app from XSS vulnerabilities, undesired tracking, malicious frames, and much more. CSP works by defining a whitelist of content sources which are trusted. You can configure them for each kind of resource a web page may need (script, stylsheets, fonts, frames, media, and so on...) There are multiple directives available, so a website owner can have a granular control. See HTML 5 Rocks, KEYCDN for more details. Unfortunately CSP is unsopported by older browser.
  By defaul, directives are wide open, so it's important to set the defaultSrc directive as a fallback. Helmet supports both defaultSrc and default-src naming styles. The fallback applies for most of the unspecified directives. In this exercise, use helmet.contentSecurityPolicy(), and configure it setting the defaultSrc directive to ["self"] (the list of allowed sources must be in an aray), in order to trust only your website address by default. Set also the scriptSrc directive so that you will allow scripts to be downloaded from your website, and from the domain "trusted-cdn.com"
  ```python
  app.use(helmet.contentSecurityPolicy({
    directives: {
      defaultSrc: ["'self'"],
      scriptSrc: ["'self'", 'trusted-cdn.com']
    }
  }));
  ```
  ### Configure Helmet Using the "parent" helmet() Middleware
  app.use(helmet()) will automatically include all the middleware introduced above, except noCache(), and contentSecurityPolicy(), but these can be enabled if necessary. You can also disable or configure any other middleware individually, using a configuration object.
  ```python
  // example
  app.use(helmet({
    frameguard: { // configure
      action: 'deny'
    },
    contentSecurityPolicy: { // enable and configure
      directives: {
        defaultSrc: ["self"],
        styleSrc: ['style.com'],
      }
    },
    dnsPrefetchControl: false // disable
  }));
  ```
