# WAHH NOTES

These are notes for WAHH 2nd edition. While much of the begining material will be familiar to those who have developed for the web, it is still a good review.

## Chapter 1

1. The core security problem is USERS CAN SUBMIT ARBITRARY INPUT!
2. Most developers make serious assumptions about the security of the packages that they are using to build applications.
3. Vulns in a framework affect all the places that framework exists.
4. Hardened servers and firewalls must allow http/s connections so you have a wide open attack surface to send crafted input. The data sails past the network defenses in the same way ordinary and benign data does.

## Chapter 2

### Main security mechanisms:

1. Authentication
2. Session Management
3. Access Control

### Handling User Input

1. Reject known bad – the most inferior
	2. Accept known good – Pretty good but doesn’t account for all situations (e.g. someone that needs a hyphen in their name)
	3. Sanitization – very good but effective sanitization may be difficult to achieve if several kinds of potentially malicious data need to be accommodated within one item.
2. Numerous blacklist-based filters are vulnerable to NULL byte (%00) attacks.
	1. Inserting a NULL byte anywhere before a blocked expression can cause some filters to stop processing the input.
3. SQL injection can be prevented through the correct use of parameterized queries for database access.
4. Other situations can be avoided if the application is designed not to pass user supplied input directly to the operating sytsem command interpreter.
5. Syntactic validation doesn’t help in situations where malicious input looks exactly like non-malicious input. For example, when an attacker wants to access another user’s bank account. This looks like a regular request syntactically – but is clearly malicious – This is where Boundary Validation comes in.
6. Boundary Validation
	1. Validation across trust boundaries.
	2. See page 27 for a fantastic example.
7. If sanitization isn’t being done recursively you could allow an attacker to craft something that is malicious AFTER the stripping happens (e.g. <scr<script>ipt>)
8. Canonicalization – the process of converting or decoding data into a common character set.
	1. Double URL encoding can be used in some cases to defeat. https://www.owasp.org/index.php/Double_Encoding
	2. If certain characters (even encoded ones) are stripped (non-recursively) you can use the same attack as above with <scr<script>ipt> e.g. %%2727.
	3. Explore the xss technique on p. 29.
	4. Don’t forget character mapping based on best fit p. 29. you can use characters with diacritical marks to smuggle characters past the input filters.

### Handling Attackers

1. Handling Errors
2. Maintaining audit logs
3. Alerting Administrators
4. Reacting to attacks

### Handling Errors

Errors should be handled and not dump verbose messages to the UI that an attacker could leverage to launch an attack.

### Maintaining Audit Logs

1. All events realting to the authentication functionality such as successful and failed login, and change of password
2. Key transactions, such as credit card payments and funds transfers
3. Access attempts that are blocked by the access control mechanisms
4. Any requests containing known attack strings that indicate overtly malicious intentions
5. logs may be flushed to write-once media to ensure their integrity in the event of a successful attack
6. These audit logs would provide a gold mine for any attacker.

### Alerting Admins

1. Alerts can’t be so frequent that they are ignored but roll up a lot of signatures (large number of requests from single IP, unusually large amount of funds being transferred, things that can’t normally be modified by the user unless using a proxy) and create one alert that they can react to.
Reacting to Attacks
	1. Slower responses to requests
	2. Terminate session
	3. Generally these are to frustrate the attacker to slow them down so admins can take action

## Chapter 3

1. HTTP response – Server header contains a banner indicating the web server software being used and sometimes other details such as installed modules and the server operating system. This may or MAY NOT be accurate.
2. Other HTTP methods:
	1. TRACE – returns the exact message it received. This can be used to detect the effect of any proxy servers between the client and serve that may manipulate the request.
	2. OPTIONS – reports the HTTP methods that are available for a particular resource. This usually lists the available methods in the Allow header.
	3. PUT – attempts to upload the specified resource to the server using the content contained in the body of the request.

### Cookies

The response Set-Cookie header can include optional attributes. One of the most interesting is HttpOnly, which means that the cookie cannot be directly accessed via client-side JavaScript.

### Status Codes

1. General
	1. 1xx – Informational
	2. 2xx – Successful
	3. 3xx – The client is redirected to a different resource
	4. 4xx – The request contains an error of some kind.
	5. 5xx – The server encountered an error fulfilling the request.
2. Specific
	1. 201 – Created – in response to a PUT request.
	2. 3xx
		1. 301 – Moved Permanently – redirects the browser permanently to a different URL, which I s specified in a Location header.
		2. 302 – Found – redirects temporarily to a URL in the Location header.
		3. 304 – Not Modified – browser to use cached copy of the requested resource. The server uses the If-Modified-Since and If-None-Match to determine if the client has the latest version of the resource.
	3. 4xx
		1. 400 – Bad Request – invalid HTTP request. Probably will encounter this if you have modified a request in certain invalid ways.
		2. 401 – Unauthorized indicates that the server requires HTTP auth before the request will be granted. The WWW-Authenticate header contains details on the type(s) of auth supported.
		3. 403 Forbidden – no one is allowed to access this.
		4. 404 Not Found
		5. 405 Method Not Allowed – indicates that the method used in the request is not supported for the specified URL.
		6. 413 Request Entity Too Large – You may see this if you are probing for buffer overflow vulnerabilities in native code. The request could be too large for the server to handle.
		7. 414 Request URI Too Long is similar to 413.
	4. 5xx
		1. 500 – Internal Server Error – these could be helpful if your request caused an unhandled exception to happen.
		2. 503 – Service Unavailable – app not responding.

### Web Functionality

1. HTTP requests can be used to send parameters in four ways.
	1. In the URL query string
	2. Cookies
	3. File path of rest URLs
	4. Body of POST requests

### Encoding

1. URLs are only permitted to contain the printable characters in the US ASCII character set – that is ASCII code in the range of 0x20 to 0x7e inclusive.
2. Problematic characters in URLs are encoded in the following ways:
	1. %3d – =
	2. %25 – %
	3. %20 – Space – could also be represented by a +
	4. %0a – New Line
	5. %00 – Null byte
3. For the purpose of attacking web applications, you should URL-encode any of the following characters when you insert them as data into an HTTP request:
	1. space % ? & = ; + #
4. 16 bit Unicode encoding starts with %u followed by the character’s Unicode code point expressed in hexadecimal.
5. UTF-8 is a variable length encoding standard.
6. Unicode encoding is primarily of interest when attacking a web application because it can be used sometimes to defeat input validation mechanisms.
7. Any character can be HTML encoded using its ASCII code in decimal form. &#34; – “ or by using its ASCII code in hexadecimal form prefixed by an x: &#x22; — "

## Chapter 4 – MAPPING THE APPLICATION

Careful when spidering as some applications don’t protect their admin actions and you could end up deleting or wrecking whole parts of an application by following actions and tossing random data at them.

>Here is where looking into Damn Vulnerable Web App, Google Gruyere etc. is would be helpful. Perhaps look into Docker as well for these.

>Seems like Google Guryere is the quickest to get started with. OWASP Juice Shop is written in AngularJS but looks like they have plans to upgrade to the newest Angular this Summer.

Brute forcing urls is something you can do with Intruder in Burp but you could easily do this with a script using a word list. You will want to do this recursively as you find new content.

For instance, if you find a resource called /auth/ForgotPassword you may find as well try /auth/ResetPassword etc.

Also, notice that the last part of the resource used capital letters and you will want to follow the naming conventions set up by the app to help find hidden content.

If you haven't paid for Burp pro you can work on automating some of the intruder attacks with scripts.

In the word list it is worth using extensions like txt, bak, src, inc, old etc. which may include source backup pages of live pages.

Use extensions associated with the development language in use as well.

Also worth looking for .tmp files of different sorts.

Burp Suite Pro has some epic content discovery features. However, it is worth doing some of this by hand and through scripts for understanding purposes.

> Also worth checking out DirBuster from OWASP for content discovery.

### Use of public information

Make a list of all known people and emails from the target. You can then search for these on things like stack overflow to see if they are asking questions that would compromise the security of the applictaion. You may find some of these names etc. within the website, commented in sourcecode etc.

Indexed Content: Use search engines and Web Archives to find previously archived sensative information that is no longer on the site.

Old functionality that is no longer available in the application may still be alive on the back end and hackable.

### Leveraging the Web Server

WIKTO can be used to find known vulns in a framework as well as resources you may not find any other way.

WIKTO & NIKTO do return false positives as well as false negatives as they try to evaluate the results returned to them. It is preferable to use BURP INTRUDER as you can interpret the raw response and not rely on the tool interpreting for you.

> Install NIKTO or WIKTO and explore.

### Application Pages Versus Functional Paths

Mapping an application based on pages only gives a partial picture. Per the example in the book a single URL may be the base for several application functions when provded with params for servlet, accout etc. 

Mapping an application based on functionality will help paint a clearer picture of what vulns there may be. If a single URL is used for many functions by passing the name of a function as a parameter it is important not to miss these.


### Discovering Hidden Parameters

A good example is when a url uses a flag like ```debug=true```.
The only way to find these hidden parameters is to 'guess' them but you can automate these guesses quite easily.

Burp Intruder can be used to do this using the "cluster bomb" attack type.

### Request Parameters

These may not follow the obvious query string or REST url parm structures. Analyze the URLs closely so you can figure out what scheme the application is using.

### HTTP Headers

Some appliciations may use referer headers or User-Agent headers to dynamically add content such as HTML keywords, containing strings that recent visitors from search engines have been searching for and you could exploit this by making repeated requests using crafted headers. 

Spoofing the User-Agent header you can access different experiences (mobile vs. desktop) and perhaps get access to a less secure user interface.

Developers may assume some Headers are untainted like X-Forwarded-For and you may be able to deliver an SQL injection or persistant cross-site scripting via this header. This header may be used in cases where the application resides behind a load balnacer or proxy and may use the IP address in that header thinking that it is safe instead of using the platform APIs for IP address.

### Out-of-Band Channels

Some examples of web applications that receive user controllable data via an out-of-band channel are:

1. A web mail application that processes and renders e-mail messages received via SMTP.
2. A publishing application that contains a function to retrieve content via HTTP from another server.
3. An intrusion detection application that gathers data using a network sniffer that presents this using a web application interface.
4. Any kind of application that provides an API interface for use by non-browser user agents, such as cell phone apps, if the data processed via this interface is shared with the primary web application.

### Identifying Server-Side Technologies

1. Banner Grabbing - using the Server header in a response to fingerprint the server etc.
2. The version of software may be disclosed in Templates used to build HTML pages, Custom HTTP headers or URL query string params.

### HTTP Fingerprinting

In principle, any item of information returned by the server may be customized or falsified and banners like the Server header are no exception.

Despite these measueres it is usually possible for a determined attacker to use other aspects of the web server's behavior to determine the software in use.

> Httprecon is a handy tool that performs a number of tests in an attempt to fingerprint a web server's software.

### File Extensions

File extensions used within URLs often disclose the platform or programming language.

However, if there are no file extensions within the app that you see you can still use some of the default functionality of the software to fingerprint. For instance, if you request a nonexistent file with a .aspx extension it may return a customized error message provided by the ASP.NET framework.

> httprint tool can be used to fingerpirnt the web server as well

### Mapping the application

1. Client-side validation - checks may not be replicated on the server.
2. Database Interaction - SQL injection
3. File Uploading and Downloading - Path traversal vulns, stored cross site scripting.
4. Display of user supplied data - xss
5. Dynamic Redirects - redirection and header injection attacks
6. Social networking features - username enumeration, stored xss
7. Login - username num, weak passwords, ability to use brute force
8. Multistage Login - logic flaws. 
9. Session State - Predictable tokens, insecure handling of tokens.
10. Access Controls - horizontal and vertical priv escalation
11. User impersonation  functions - priv escalation
12. Use of cleartext communications - session hijacking, captrue of creds and other sensitive data.
13. Off-site links - leakage of query string params in the Referer header.
14. Interfaces to external systems -Shortcuts in the handling of sessions and/or access controls.
15. Error messages - Information leakage
16. E-mail interaction - E-mail and/or command injection
17. Native code components or interaction -  Buffer overflows.
18. Use of third-party application components - Known Vulns
19. Identifiable web serverr sofware - Common config weaknesses, known software bugs

> Always check third-party code against public vuln databases such as www.osvdb.org to determine known issues.


## Chapter 5 - Bypassing Client-Side Controls

### Transmitting Data Via the Client

Many applications will trasmit data to the client which the client will then pass through un subsequent requsts for a cople differnt reasons. However, this causes countless vulnerabilities that the devlllers will have to be cognizant of.

### Hidden Form Fields

If the data has been obfuscated in some way, don't forget that if you know what the data should be you may be able to decipher the obfuscation algorithm.

Also, you may be able to find another function in the application that you can feed the obfuscated data to and get the deobfuscated data back.

Even better, you may not need to deobfuscate it at all. If you have a "pricing_token" that represents $100 dollars and you can get a token for the $10 dollar item it can be exploited without knowing how to decypher the actual token at all.

### ASP.NET ViewState

Commonly encounterd mechanism for transmitting opaque ata via the client. Hidden field that is created by default in all ASP.NET apps. It contains serialized information about the state of the current page.

The ViewState parmeter is just a Base64-encoded string that be easily decoded.

> Because of how Base64 encoding works if you start at the wrong place you will get gibberish. If you get gibberish, start at a different place. Try starting from four adjacent offsets into the encoded string.

_Burp has a ViewState parser that indicates if it is MAC protected_

### Capturing User Data: HTML Forms

If you intercept a response (instead of a request) you may need to remove the If-Modified-Since or If-None-Match headers so you aren't changing the cached version in Burp.

Don't forget that if, in the case of a "maxlength" property that isn't being validated on the server you may be able to do all kinds of things like SQL injection etc. and not just modifying the request past the length.

### Script-Based Validation

Point blank, if the applications isn't validating things on the server and only on the client -- everything can be modified and they are screwd.

### Disabled Form Elements

IMPORTANT - browsers do not include disabled form elemnts when forms are submitted -- so if you are automatting an attack you need to enable these elements for them to send actual data.

### Capturing User Data: Browser Extensions

>Even though they talk about this with respect to Java Applets anywhere that a platform is serializing data you may be able to deserialize using that language's tools. For example how DSer for Burp deserializes serialized Java Objects. 

The following is from the book and it is worth looking into how CA certifciates work...

```
The second problem is that the client component may not accept the SSL
certificate being presented by your intercepting proxy. If your proxy is using a generic self-signed certifi cate, and you have configured your browser to accept it, the browser extension component may reject the certifi cate nonetheless. This may be because the browser extension does not pick up the browser’s configuration for temporarily trusted certifi cates, or it may be because the component itself programmatically requires that untrusted certificates should not be accepted. In either case, you can circumvent this problem by confi guring your proxy to use a master CA certificate, which is used to sign valid per-host certifi cates for each site you visit, and installing the CA certificate in your computer’s trusted certificate store. See Chapter 20 for more details on how to do this.
```
If you are working with something that isn't using HTTP you may need to modify requests using a network sniffer or a function hooking tool.

> One example is Echo Mirage.

While a lot of this refers to Java Applets, Flash, and Sliverlight -- which are not really that useful at this point, the techniques are applicable in more places where you need to really interact with code instead of just with responses. 

For instance, the section on byte code obfuscation could be applied to some javascript obfuscation techniques. Look past the technologies and look into the techniques.

### For dealing with native applications

You can use Ida Pro to turn native executable code into human-readable assembly.

There is a whole slew of books for learning reverse engineering.

## Chapter 6 - Attacking Authentication

Sometimes admin passwords are weaker than regular user passwords because those are usually the first users and their passwords may be set up before the validations are in place. They also may not have anything in common with the validation rules that a regular user has.

If the app tries to log failed login attempts through a cookie that is easy to bypass. If it is stored in the session you can just exclude the session cookie so that you keep getting a new session and a brand new login slate!

Many sites now use general error messages so you can't enumerate users but some don't. Also, allowing users to pick their own username makes it hard for an app to avoid username enumeration.

Forgot password functionality has gotten better about not allowing user enumeration but still can be a weak spot.

Even responses that look simlar onscreen can have subtle differences in html that might give you a hint on if you have hit a valid username.You can use the comparer tool in Burp Suite.

With regard to password reset -- if the application does in fact send an email with a recovery URL - sign up for a number of a counts and get a bunch of these urls to see if you can guess any pattern.

Don't forget to examine Remember Me functionality

### User Impersonation Functionality

Example: some banking applications allow helpdesk operators to verbally authenticate a telephone user and then switch their application session into that user's context to assist them.

Sometimes it is implemented as a simple back door.

Even if this functionality is not specifically linked to it can still be uncovered. (Application pages vs. Functional Paths)

### Incomplete Validation of Credentials

Try various versions of your passwords to see if characters are being stripped out, case is being changed, if they are only checking the first n characters etc.

You can then use the feedback to revise your brute force password list.

### Predictable Initial Passwords

These are most common on intranet-based corporate applications that default to a certain password for new employees etc.

### Fail-Open Login Mechanisms

It is a logic flaw with serious consequences.

### Defects in Multistage Login

There are a multitude of ways to test multistage login. Try submitting steps out of order. Check to see if user creds are submitted more than once, or if there are flags that indicate what stages you have finished etc.

For multi stage login the you should catch all errors and handle them in a way that kills the session BUT you should take the user through all the steps so they don't know hat information was the problem. 
