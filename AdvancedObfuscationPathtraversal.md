
---

# Advanced Directory Traversal filter bypassing #

---


A directory traversal (or path traversal) consists in exploiting insufficient security validation/sanitization of user-supplied input file names, so that characters representing "traverse to parent directory" are passed through to the file APIs. The goal of this attack is to order an application to access a computer file that is not intended to be accessible. This attack exploits a lack of security (the software is acting exactly as it is supposed to) as opposed to exploiting a bug in the code.

## What really is a Directory Traversal ##

A Path Traversal attack aims to access files and directories that are stored outside the web root folder. By browsing the application, the attacker looks for absolute links to files stored on the web server. By manipulating variables that reference files with “dot-dot-slash (../)” sequences and its variations, it may be possible to access arbitrary files and directories stored on file system, including application source code, configuration and critical system files, limited by system operational access control. The attacker uses “../” sequences to move up to root directory, thus permitting navigation through the file system. But what exactly is a Path Traversal vulnerability?

A Path Traversal is two things:

  1. An input validation vulnerability (e.g the application allows the dot-dot-slash characters to pass through).
  1. An access control vulnerability (e.g. the application has access to the OS file system with high privileges).

**Note:** OWASP categorizes this type of attack as a hybrid attack, meaning access control and input validation issue.

## Fuzzing for Directory Traversal ##

If your initial attempts to perform a traversal attack are unsuccessful, this does not mean that the application is not vulnerable. Many application developers are aware of path traversal vulnerabilities and implement various kinds of input validation checks in an attempt to prevent them. However, those defenses are often flawed and can be bypassed by a skilled attacker.The first type of input filter commonly encountered involves checking whether the filename parameter contains any path traversal sequences. If it does, the filter either rejects the request or attempts to sanitize the input to remove the sequences. This type of filter is often vulnerable to various attacks that use alter- native encodings and other tricks to defeat the filter.

## Using unicode/UTF-8 encoding ##

UTF-8 encoding is a variable-width encoding that can represent every character in the Unicode character set. It was designed for backward compatibility with ASCII and to avoid the complications of endianness and byte order marks in UTF-16 and UTF-32. UTF-8 was noted as a source of vulnerabilities and attack vectors by Bruce Schneier and Jeffrey Streifling. When Microsoft added Unicode support to their Web server, a new way of encoding ../ was introduced into their code, causing their attempts at Directory Traversal prevention to be circumvented, this technique can also be used to bypass Web Application Firewalls.

Multiple percent encodings, such as the characters below translate into the / and \ characters:

  1. %c1%1c
  1. %c0%af

**Note:**  In older versions of Windows IIS web server, applications would, by default, run with local system privileges, allowing access to any readable file on the local filesystem. In more recent versions, in common with many other web servers, the server’s process by default runs in a less privileged user context. For this reason, when probing for path traversal vulnerabilities, it is best to request a default file that can be read by any type of user, such as c:\windows\win.ini.

## Using unicode/UTF-7 encoding ##

UTF-7 (7-bit Unicode Transformation Format) is a variable-length character encoding that was proposed for representing Unicode text using a stream of ASCII characters. It was originally intended to provide a means of encoding Unicode text for use in Internet E-mail messages that was more efficient than the combination of UTF-8 with quoted-printable.

UTF-7 was first proposed as an experimental protocol in RFC 1642, A Mail-Safe Transformation Format of Unicode. This RFC has been made obsolete by RFC 2152, an informational RFC which never became a standard. As RFC 2152 clearly states, the RFC "does not specify an Internet standard of any kind". Despite this RFC 2152 is quoted as the definition of UTF-7 in the IANA's list of charsets. Neither is UTF-7 a Unicode Standard. The Unicode Standard 5.0 only lists UTF-8, UTF-16 and UTF-32. There is also a modified version, specified in RFC 2060, which is sometimes identified as UTF-7.

  1. Dot: .
  1. Forward slash: /
  1. Backslash: \

**Note:** UTF-7 allows multiple representations of the same source string. In particular ASCII characters can be represented as part of Unicode blocks. As such if standard ASCII based escaping or validation processes are used on strings that may be later interpreted as UTF-7 then Unicode blocks may be used to slip malicious strings past them. To mitigate this problem systems should perform decoding before validation and should avoid attempting to auto-detect UTF-7.Older versions of Internet Explorer can be tricked into interpreting the page as UTF-7. This can be used for a cross-site scripting attack as the < and > marks can be encoded as +ADw- and +AD4- in UTF-7, which most validators let through as simple text.

## Using overlong UTF-8 unicode encoding ##

The UTF-8 standard specifies that the correct encoding of a UTF-8 codepoint use only the minimum number of bytes required to hold the significant bits of the codepoint. Longer encodings are called overlong and are not valid UTF-8 representations of the codepoint. This rule maintains a one-to-one correspondence between codepoints and their valid encodings, so that there is a unique valid encoding for each codepoint. The following text shows how overlong UTF-8 encoding can be applied to Directory Traversal payload characters:

  1. Dot: %c0%2e, %e0%40%ae, %c0ae
  1. Forward slash: %c0%af, %e0%80%af, %c0%2f
  1. Backslash: %c0%5c, %c0%80%5c

**Note:** You can use the illegal Unicode payload type within Burp Intruder to generate a huge number of alternate representations of any given character and submit this at the relevant place within your target parameter. These representations strictly violate the rules for Unicode representation but nevertheless are accepted by many implementations of Unicode decoders, particularly on the Windows platform. For more information UTF-8 encodings and UTF-8 codepoints see http://en.wikipedia.org/wiki/UTF-8#Overlong_encodings.

To summarize the previous section: a Unicode string is a sequence of code points, which are numbers from 0 to 0x10ffff. This sequence needs to be represented as a set of bytes (meaning, values from 0-255) in memory. The rules for translating a Unicode string into a sequence of bytes are called an encoding.

## Using URL encoding ##

Percent-encoding, also known as URL encoding, is a mechanism for encoding information in a Uniform Resource Identifier (URI) under certain circumstances. Although it is known as URL encoding it is, in fact, used more generally within the main Uniform Resource Identifier (URI) set, which includes both Uniform Resource Locator (URL) and Uniform Resource Name (URN). As such, it is also used in the preparation of data of the application/x-www-form-urlencoded media type, as is often used in the submission of HTML form data in HTTP requests. Some web applications scan query string for dangerous characters such as .., ..\ and ../, but fail to prevent Directory Traversal in URL encoded inputs. Therefore these applications are vulnerable to percent encoded Directory Traversal such as:

  1. %2e%2e%2f which translates to ../
  1. %2e%2e/ which translates to ../
  1. ..%2f which translates to ../
  1. %2e%2e%5c which translates to ..\

**Note:** Percent encodings were decoded into the corresponding 8-bit characters by Microsoft webserver. This has historically been correct behavior as Windows and DOS traditionally used canonical 8-bit characters sets based upon ASCII. However, the original UTF-8 was not canonical, and several strings were now string encodings translatable into the same string. Microsoft performed the anti-traversal checks without UTF-8 canonicalization, and therefore not noticing that (HEX) C0AF and (HEX) 2F were the same character when doing string comparisons. Malformed percent encodings, such as %c0%9v was also utilized.

## Using double URL encoding ##

Double URL encoding a Directory Traversal payloads would also have similar side effects as with the SQL Injection payload double URL encoding, as far as the Web Application filter or firewall is concerned. Double URL-encoded representations of traversal sequences would look like this:

  1. Dot: .
  1. Forward slash: %252F
  1. Backslash: %255C

**Note:** You can either use the Teenage Mutant Ninja Turtles Project to do these mutations, Burp Decoder or an online URL encoder at http://meyerweb.com/eric/tools/dencoder/.

## Using 16-bit Unicode encoding ##

UTF-16 (16-bit Unicode Transformation Format) is a character encoding for Unicode capable of encoding 1,112,064 numbers (called code points) in the Unicode code space from 0 to 0x10FFFF. It produces a variable-length result of either one or two 16-bit code units per code point. UTF-16 is officially defined in Annex Q of the international standard ISO/IEC 10646. It is also described in "The Unicode Standard" version 2.0 and higher, as well as in the IETF's RFC 2781. UTF-16 encoding produces a sequence of 16-bit code units. Each unit thus takes two 8-bit bytes, and the order of the bytes may depend on the endianness (byte order) of the computer architecture. The following text shows someone represent path traversal characters on UTF-16:

  1. Dot: %u002e
  1. Forward slash: %u2215
  1. Backslash: %u2216

**Note:** UTF-16 is used for text in the OS API in Microsoft Windows 2000/XP/2003/Vista/CE. In Windows files and network data tend to be a mix of UTF-16, UTF-8, and legacy byte encodings. For instance the registry is a byte encoding, and Windows will often display filenames from remote systems as byte encodings, resulting in mojibake (mojibake means presentation of incorrect, unreadable characters when software fails to render text correctly according to its associated character encoding.) if in fact they are UTF-8.

## Using NULL bytes ##

Some applications check whether the user-supplied filename ends in a particular file type or set of file types and reject attempts to access anything else. Sometimes this check can be subverted by placing a URL- encoded null byte at the end of your requested filename, followed by a file type that the application accepts.

Example: ../../../../../passwd%00.jpg

**Note:** The reason this attack sometimes succeeds is that the file type check is implemented using an API in a managed execution environment in which strings are permitted to contain null characters (such as String. endsWith() in Java). However, when the file is actually retrieved, the application ultimately uses an API in an unmanaged environment in which strings are null-terminated. Therefore, your file name is effectively truncated to your desired value.

## Using Mangled paths ##

If the application is attempting to sanitize user input by removing tra- versal sequences and does not apply this filter recursively, it may be possible to bypass the filter by placing one sequence within another.

Examples:....//, ...\\//, ..//..//..\\

**Note:** Mangled paths can be used also as an attempt to crush the Web Application.

## Methods to prevent Directory Traversal ##

In order to avoid Path Traversal attacks you should follow the generic steps shown below:

  1. Follow the principle of least privilege - every module must be able to access only the information and resources necessary to its legitimate purpose.
  1. Follow the principle of least user access or least-privileged user account (LUA), the concept that all users and modules at all times should run with as few privileges as possible.
  1. Consider the use of read-only file systems (such as CD-ROM or locked USB key) if practical.The application can also mitigate the impact of most exploitable path traversal vulnerabilities by using a chrooted environment to access the directory containing the files intended to be accessed  by the malicious user. In this situation, the chrooted directory is treated as if it is the file system root, and any redundant traversal sequences that attempt to step up above it are ignored.
  1. Avoid passing user-submitted data to any file system API.
  1. Analyze user submitted input by performing relevant decoding and canonicalization of the user submitted file-name. The application should check whether this contains  path traversal sequences (using backward or forward slashes) or any null bytes. If so, the application should stop processing the request. It should not attempt to perform any sanitization on the malicious file-name.
  1. The application should also use a hard-coded list of permissible file types and reject any request for a different type (after the preceding decoding and canonicalization has been performed).
  1. If file uploads must be allowed in Java, this can be achieved by instantiating a java.io.File object using the user-supplied filename and then calling the getCanonicalPath method on this object. If the string returned by this method does not begin with the name of the start directory, then the user has somehow bypassed the application’s input filters, and the request should be rejected.

**Note:** Chrooted file systems are supported natively on most Unix-based platforms. A similar effect can be achieved on Windows platforms (in relation to traversal vulnerabilities) by mounting the relevant start directory as a new logical drive and using the associated drive letter to access its contents.

The application should integrate its defenses against path traversal attacks with its logging and alerting mechanisms. Whenever a request is received that contains path traversal sequences, this indicates likely malicious intent on the part of the user, and the application should log the request as an attempted security breach, terminate the user’s session, and if applicable, suspend the user’s account and generate an alert to an administrator.