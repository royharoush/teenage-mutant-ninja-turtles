
---

# What is a fuzzer #

---


Fuzzer: A fuzzer is a program that attempts to discover security vulnerabilities by sending random input to an application. If the program contains a vulnerability that can lead to an exception, crash or server error (in case you are fuzzing a web app), it can be determined that a vulnerability has been discovered. Fuzzers are often termed Fault Injectors for this reason, they generate faults and send them to an application. Generally fuzzers are good at finding buffer overflows, DoS, SQL Injection, XSS, and Format String bugs. They do a poor job at finding vulnerabilites related to information disclosure, encryption flaws and any other vulnerability that does not cause the program to crash.

## Fuzzer payloads and teenage-mutant-ninja-turtles ##

teenage-mutant-ninja-turtles is also a comprehensive set of known attack pattern sequences, predictable locations, and error messages for intelligent brute force testing and exploit condition identification of web applications.Many mechanisms of attacks used to exploit different web server platforms and applications are triggered by particular meta-characters that are observed in more than one product security advisory. teenage-mutant-ninja-turtles is a database with attack patterns known to have caused exploit conditions in the past, categorized by attack type, platform, and application.

## Burp Intruder as a Fuzzer ##

Burp Intruder is a tool for automating customized attacks against web applications. With Burp Intruder you can identify and exploit all kinds of web security vulnerabilities. Burp Intruder is exceptionally powerful and configurable, and its potential is limited only by your skill and imagination in using it. You can use Burp Intruder to:

  1. Perform fuzzing of a web application to identify common vulnerabilities, such as SQL injection, cross-site scripting, and buffer overflows.
  1. Enumerate identifiers used within the application, such as account numbers and usernames.
  1. Deliver customized brute-force attacks against authentication schemes and session handling mechanisms.
  1. Exploit bugs such as broken access controls and information leakage to harvest sensitive data from the application.
  1. Perform highly customized discovery of application content in the face of unusual naming schemes or retrieval methods.
  1. Carry out concurrency attacks against race conditions, and application-layer denial-of-service attacks.

A typical workflow using Burp Intruder is as follows:

  1. Identify an interesting or vulnerable request within any of the Burp Suite tools, and send this to Intruder.
  1. Mark the locations in the request where you want to insert payloads.
  1. Configure your attack payloads, using Intruder's highly configurable algorithms and preset lists, or your own custom list of payloads.
  1. Start the attack and review the detailed results, including all requests made and responses received.
  1. Analyze the results to achieve your chosen objective, using customizable filtering and sorting, or by defining your own rules for matching or extracting response data.

## Loading teenage-mutant-ninja-turtles to Burp ##

First we mutate a payload with teenage-mutant-ninja-turtles by following the steps below:

**Example:** Script execution for mutating post fixed SQL injection payload file:

|root# ./tmntv(version).py|Command prompt input|
|:------------------------|:-------------------|
|Usage: Type a single option then press enter type filename press enter again for help type help for the turtle type ban!|Screen output       |
|Enter option: pfx        |Screen output       |
|Enter filename: filename.txt|Screen output       |
|Payload is being generated please wait...|Screen output       |
|Payload mutation is finished enjoy...|Screen output       |
|Bye bye...               |Screen output       |


Then in order to load teenage-mutant-ninja-turtles to burp you have to go:

|Burp Intruder TAB -> payloads TAB -> Click load button|
|:-----------------------------------------------------|


> ![http://4.bp.blogspot.com/-rnROxAkdPAE/T4MiWSFPXCI/AAAAAAAAAIw/HSvMbjjgQV8/s1600/burp.gif](http://4.bp.blogspot.com/-rnROxAkdPAE/T4MiWSFPXCI/AAAAAAAAAIw/HSvMbjjgQV8/s1600/burp.gif)

Now that we mutated the payload file we have to feed that into Burp Intruder in order to use it later on:

> ![http://1.bp.blogspot.com/-2egGZIwzcOk/T4MipDygvGI/AAAAAAAAAI4/L58kyrH89CU/s1600/burp1.gif](http://1.bp.blogspot.com/-2egGZIwzcOk/T4MipDygvGI/AAAAAAAAAI4/L58kyrH89CU/s1600/burp1.gif)

After we do that we point the payloads to the proper value:

> ![http://4.bp.blogspot.com/-J3lWJTrsbmA/T4MjPcpvl8I/AAAAAAAAAJA/Qg4mtgfyapU/s1600/burp3.gif](http://4.bp.blogspot.com/-J3lWJTrsbmA/T4MjPcpvl8I/AAAAAAAAAJA/Qg4mtgfyapU/s1600/burp3.gif)

**Note:** If you see carefully the above image you will see that I am using the sniper mode for the test (best choice when trying to identify initial SQL injection vulnerabilities)

## Different modes of Burp Intruder ##

The "attack type" drop-down menu is used to define a key aspect of the behaviour of Burp Intruder - the way in which payloads are placed into the specified positions to form individual requests. The four possible attack types are described below:

  * **sniper** - This uses a single set of payloads. It targets each position in turn, and inserts each payload into that position in turn. Positions which are not targeted during a given request are not affected - the position markers are removed and any text which appears between them in the template remains unchanged. This attack type is useful for testing a number of data fields individually for a common vulnerability (e.g. cross-site scripting). The total number of requests generated in the attack is the product of the number of positions and the number of payloads in the payload set.

**Note:** This options is very useful when you try to identify how single variable fuzzing affects the web application.

  * **battering ram** - This uses a single set of payloads. It iterates through the payloads, and inserts the same payload into all of the defined positions at once. This attack type is useful where an attack requires the same input to be inserted in multiple places within the HTTP request (e.g. a username within the Cookie header and within the message body). The total number of requests generated in the attack is the number of payloads in the payload set.

**Note:** This options is very useful when you try to identify how simultaneous variable fuzzing affects the web application, some web applications behave differently when battering ram is used instead of sniper.

  * **pitchfork** - This uses multiple payload sets. There is a different payload set for each defined position (up to a maximum of 8). The attack iterates through all payload sets simultaneously, and inserts one payload into each defined position. I.e., the first request will insert the first payload from payload set 1 into position 1 and the first payload from payload set 2 into position 2; the second request will insert the second payload from payload set 1 into position 1 and the second payload from payload set 2 into position 2, etc. This attack type is useful where an attack requires different but related input to be inserted in multiple places within the HTTP request (e.g. a username in one data field, and a known ID number corresponding to that username in another data field). The total number of requests generated by the attack is the number of payloads in the smallest payload set.

  * **cluster bomb** - This uses multiple payload sets. There is a different payload set for each defined position (up to a maximum of 8). The attack iterates through each payload set in turn, so that all permutations of payload combinations are tested. I.e., if there are two payload positions, the attack will place the first payload from payload set 1 into position 1, and iterate through all the payloads in payload set 2 in position 2; it will then place the second payload from payload set 1 into position 1, and iterate through all the payloads in payload set 2 in position 2. This attack type is useful where an attack requires different and unrelated input to be inserted in multiple places within the HTTP request (e.g. a username in one parameter, and an unknown password in another parameter). The total number of requests generated by the attack is the product of the number of payloads in all defined payload sets - this may be extremely large.

**Note:** With cluster bomb you can perform very common attacks such as login brute forcing.

## Epilogue ##

This post proved you that teenage-mutant-ninja-turtles can be used very creatively to test a web applications, you can very easily use your own mutations. If you want to test for XSS or XML Injections you have obviously to change the char-set in the relevant character set directory of the project. It would be a very good idea to add some statistical analysis and filter out your list in such a way so that more interesting characters in certain positions appear at the top of the list, meaning that it would be a good idea for example to have single quote at the begging of the string and not the end.

