
---

# Why use SQL obfuscation #

---


SQL injection attacks can be obfuscated and relatively easy bypass these bad filter implementations. Obfuscating SQL injections attacks nowadays is a de facto standard for penetration testing and has been used by well known web malware such as ASPRox. The Asprox botnet (Discovered around 2008), also known by its aliases Badsrc and Aseljo, is a botnet mostly involved in phishing scams and performing SQL Injections into websites in order to spread Malware. Since its discovery in 2008 the Asprox botnet has been involved in multiple high-profile attacks on various websites in order to spread malware. The botnet itself consists of roughly 15,000 infected computers as of May, 2008 although the size of the botnet itself is highly variable as the controllers of the botnet have been known to deliberately shrink (and later regrow) their botnet in order to prevent more aggressive countermeasures from the IT Community.

### Purpose of this project ###

This project is mostly about Obfuscated SQL Injection Fuzzing. Nowadays all high profile sites found in financial and telecommunication sector use filters to filter out all types of vulnerabilities such as SQL, XSS, XXE, Http Header Injection e.t.c. In this particular article we are going to talk only about Obfuscated SQL Fuzzing Injection attacks for now.

First of all what obfuscate means based on the Dictionary.com:

**Definition of obfuscate:** verb (used with object), _ob·fus·cat·ed, ob·fus·cat·ing._

_"To confuse, bewilder, or stupefy.To make obscure or unclear: to obfuscate a problem with extraneous information.To darken."_

Web applications frequently employ input filters that are designed to defend against common attacks, including SQL injection. These filters may exist within the application's own code, in the form of custom input validation, or may be implemented outside the application, in the form of Web application firewalls (WAF's) or intrusion prevention systems (IPS's). Usually this types of filters are called virtual patches. After you read this article should be able to understand that virtual patching is not going to protect you from advanced attackers.

### Common types of SQL Injection filters ###

In the context of SQL injection attacks, the most interesting filters you are likely to encounter are those which attempt to block any input containing one or more of the following:

  1. SQL keywords, such as SELECT, AND, INSERT
  1. Specific individual characters, such as quotation marks or hyphens
  1. White-spaces

You may also encounter filters which, rather than blocking input containing the items in the preceding list, attempt to modify the input to make it safe, either by encoding or escaping problematic characters or by stripping the offending items from the input and processing what is left in a normal way. For more information about practically bypassing filters see features section.