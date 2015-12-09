# Analysing fuzz data #

After you successfully fuzz the targeted Web Application variables, then you would have to do some analysis on the data returned back. You can analyze the results by using the Burp three different Grep features:

  1. Grep Match
  1. Grep Extract
  1. Grep Payload

**Note:** These features are found in Burp Intruder TAB -> Options TAB. The results view TAB contains several menus with commands for controlling the attack, and saving the results, server responses and the attack itself. The free version of Burp Proxy also includes these features, the only bad thing about the free version is that it will add throttling, increasing the amount of time it takes to finish the attack.

## Analyzing SQL fuzz data using Burp Intruder ##

To do that after fuzzing the variables with SQL injection payloads you go to TAB Options and then you process your results using the features mentioned above. One of the most interesting features found in Burp Intruder are the Grep Match tool. With Grep Mach you use regular expressions in order to identify and extract complex patterns of interest.

![http://1.bp.blogspot.com/-oQcdNlc6KAg/UFB5SAJExxI/AAAAAAAAA8w/qZEcB67qu_k/s1600/Results3.png](http://1.bp.blogspot.com/-oQcdNlc6KAg/UFB5SAJExxI/AAAAAAAAA8w/qZEcB67qu_k/s1600/Results3.png)

**Note:** Grep Match can help you locate the error messages within the server responses using Burp default list or your own costume error list taken from teenage mutant ninja turtles (the error file payloads). The Grep Match feature will help you to identify errors returned back from the web application, this feature is good for identifying SQL injection issues (e.g. by greping database errors), XML injection issues and Path Traversal  injection issues, but it wont be much of a help to XSS attacks. With Grep Match you can also use complex regular expressions to filter your results.

## Exploiting SQL Injections based on fuzz data and Burp Intruder ##

The most appropriate tool for exploiting SQL vulnerabilities is Grep Extract:

![http://4.bp.blogspot.com/-CKw1f7c3HLY/UFB5h5dJDFI/AAAAAAAAA84/f65LNUj_02Y/s1600/Results4.png](http://4.bp.blogspot.com/-CKw1f7c3HLY/UFB5h5dJDFI/AAAAAAAAA84/f65LNUj_02Y/s1600/Results4.png)

**Note:** Grep Extract can help you extract information from the server responses using Burp Intruder default list or your own costume list taken from teenage mutant ninja turtles (the error payload file).  Grep Extract when properly configured can mimic the behavior of famous tools such as SQLMap and Absinth to help you extract data from the database. It will save you time from writing your own script to exploit a SQL injection vulnerability.

## Analyzing XSS fuzzing results using Burp Intruder ##

The most appropriate tool for analyzing XSS fuzz data is Grep Payloads:

![http://4.bp.blogspot.com/-CJUPr0VPFoU/UFB5r_QxclI/AAAAAAAAA9A/E4dPXtBxl9g/s1600/Results5.png](http://4.bp.blogspot.com/-CJUPr0VPFoU/UFB5r_QxclI/AAAAAAAAA9A/E4dPXtBxl9g/s1600/Results5.png)

**Note:** Grep Payloads can help you match the payload information from the server responses. This feature is particularly useful for XSS attacks, because when the injected script is echoed back to the user Burp Intruder will help you locate the server response the payload was echoed back, it will also find url encoded payloads in case that is the case.

## Analyzing fuzz data using Python regular expressions ##

You can analyze fuzz data also by using the Python re module. The re module provides regular expression matching operations similar to those found in Perl. Both patterns and strings to be searched can be Unicode strings as well as 8-bit strings.Regular expressions use the backslash character ('\') to indicate special forms or to allow special characters to be used without invoking their special meaning. This collides with Python’s usage of the same character for the same purpose in string literals; for example, to match a literal backslash, one might have to write '\\\\' as the pattern string, because the regular expression must be \\, and each backslash must be expressed as \\ inside a regular Python string literal.

The solution is to use Python’s raw string notation for regular expression patterns; backslashes are not handled in any special way in a string literal prefixed with 'r'. So r"\n" is a two-character string containing '\' and 'n', while "\n" is a one-character string containing a newline. Usually patterns will be expressed in Python code using this raw string notation.It is important to note that most regular expression operations are available as module-level functions and RegexObject methods. The functions are shortcuts that don’t require you to compile a regex object first, but miss some fine-tuning parameters.

Python code example for searching XSS echoed payloads:

---

```

import re

collectedFuzzDataFile = open("fuzzDataContainer.txt","r")

collectedFuzzDataFileObj = open(collectedFuzzDataFile,"r")

if re.search(r"<script\>alert("XSS")</script>",collectedFuzzDataFileObj.read()):
        
        print "Echoed XSS payload found"

```

---


**Note:** The read() function will return back the whole file as a string, if your laptop memory cannot handle the file size (properly fuzzing an application means generating GB's of data) then you can break it into small chunks using the size value e.g. read(size).

Similar Python code you can use for searching SQL errors returned back from SQL fuzzing, the following Python code does that, the only difference is that know you are not looking for the echoed payload, you are looking for SQL error strings.

Python code example for searching SQL Errors returned back:

---

```

import re

collectedFuzzDataFile = open("sqlErrors.txt","r")

collectedFuzzDataFileObj = open(collectedFuzzDataFile,"r")

if re.search(r"ORA-00942: table or view does not exist",collectedFuzzDataFileObj.read()):
        
        print "SQL error found"

```

---


**Note:** The read() function will return back the whole file as a string, if your laptop memory cannot handle the file size (properly fuzzing an application means generating GB's of data) then you can break it into small chunks using the size value e.g. read(size).

## Epilogue ##

This post showed that you can increase payload diversity in order to have better results (meaning bypass web application filters). Obfuscated payload tests must be a de facto standard in every penetration engagement. This technique can help you understand what type of payloads you should try to inject while testing a web application and also help you to improve semi manual test by mimicking the behavior of a web application scanner.