
---

# How to use the payload mutator #

---


You can use the payload mutator the help you bypass badly implemented SQL injection filters e.g. by applying multiple layers of encoding to your payloads, applying selective payload encoding or even adding suffixes and postfixes. Either way by using the mutator you will end up increasing the payload diversity and expose the web application to a large amount of different inputs. The following sections shows you how to do that. At this point I have to say that the script is accompanied by an elegant ascii turtle banner, which I kindly added to welcome you.

## Script usage ##

In order to run the scripts you would have to have installed python, which by the way you can download from here:http://www.python.org/getit/. The tool was tested and developed using Python 2.6.1, but earlier versions higher than Python 2.5 should not have problem executing the script properly. Now in order to run the script just execute it by typing in a Linux, Unix or Mac command prompt  ./tmntv(version).py and for Windows simply type tmntv(version).py. After you do that the following usage help message will be printed to your screen:


|Usage: |Type a single option then press enter type file name press enter again for help type help for the turtle type ban!|
|:------|:-----------------------------------------------------------------------------------------------------------------|

**Note** The script will wait for an option and a file input after printing this message.

## Script options ##

The following table shows the help message along with a short explanation of each option.

| help| Print help message for providing proper script arguments|
|:----|:--------------------------------------------------------|
| sfx | This option can be used for mutating SQL injection attack strings by adding suffixes to the payloads such as EXEC, %00 e.t.c|
| pfx | This option can be used for mutating SQL injection attack strings by adding postfixes to the payloads e.g. --, );-- e.t.c|
| url | This option can be used for mutating SQL, Pathtraversal and XSS injection attack strings by url encoding the payloads|
| flr | This option can be used for mutating SQL injection attack strings by filling the gaps with SQL commends, url encoded space and other special characters|
| b64 | This option can be used for mutating SQL, Pathtraversal and XSS injection attack strings by base 64 encoding the payloads|
| hex | This option can be used for mutating SQL and Pathtraversal injection attack strings by hex encoding the payload|
| concatMSSQL| This option can be used for mutating SQL injection attack strings by adding MSSQL concatenators within payload SQL keywords|
| concatOracle| This option can be used for mutating SQL injection attack strings by adding Oracle SQL concatenators within payload SQL keywords|
| concatMySQL| This option can be used for mutating SQL injection attack strings by adding MySQL concatenators within payload SQL keywords|
| utf8| This option can be used for mutating Pathtraversal attack strings by utf-8 encoding|
| utf16| This option can be used for mutating Pathtraversal attack strings by utf-16 encoding|
| utf32| This option can be used for mutating Pathtraversal attack strings by utf-32 encoding|
| gzip| This option can be used for mutating Pathtraversal attack strings by gzip encoding (Not tested thoroughly on Web Applications)|
| bzip| This option can be used for mutating Pathtraversal attack strings by bzip encoding (Not tested thoroughly on Web Applications)|
| ver | Print version of the script                             |
| ban | Print ThE script kiddy banner                           |
| allsql| Perform all SQL string injection mutations to the file provided as an input|
| XXSMe| Converts the payload given as an input to an xml file that XXSMe can import|
| SQLInjectMe| Converts the payload given as an input to an xml file that SQLInjectMe can import|
| var | This option can be used for mutating SQL injection attack strings by adding case variation to the payloads|
| clean| Clean payload the whole database from trailing,leading spaces,blank lines and double payloads|
| ded | Deduplicate all attack strings payloads to the file provided as an input|
| allxss| Collect all xss payloads from the payload database into a single file|
| allmssql| Collect all mssql payloads from the payload database into a single file|
| allaccess| Collect all payloads from the payload database into a single file|
| allpostgre| Collect all payloads from the payload database into a single file|
| alloracle| Collect all oracle payloads from the payload database into a single file|
| allldap| Collect all ldap payloads from the payload database into a single file|
| allxxe| Collect all external entity payloads from the payload database into a single file|
| allssi| Collect all server side include payloads from the payload database into a single file|
| allcmd| Collect all os command payloads from the payload database into a single file|
| alluseragent| Collect all useragent payloads from the payload database into a single file|
| allpaths| Collect all path traversal payloads from the payload database into a single file|
| allxpath| Collect all XPath payloads from the payload database into a single file|
| allxml| Collect all xml payloads from the payload database into a single file|
| allerrors| Collect all error payloads from the payload database into a single file|
| allhttpmethods| Collect all http method payloads from the payload database into a single file|
| isdangerous| TODO Print warning if the payload file contains dangerous database SQL keywords|
|Now fuzz an application...|

## Script Examples ##

**Example 1:** Script execution for generating suffixed SQL injection payload file:

|root# ./tmntv(version).py|Command prompt input|
|:------------------------|:-------------------|
|Usage: Type a single option then press enter type filename press enter again for help type help for the turtle type ban!|Screen output       |
|Enter option: sfx        |Screen output       |
|Enter filename: filename.txt|Screen output       |
|Payload is being generated please wait...|Screen output       |
|Payload mutation is finished enjoy...|Screen output       |
|Now fuzz an application...|Screen output       |


**Note:**The tool after properly executed will generate a file named after the mutation type and the current time so you can track the generated payloads, the file name would look like this Tue04Sep18\_15\_16\_suffixedPayloads.lst.

**Example 2:** Script execution for generating post fixed SQL injection payload file:

|root# ./tmntv(version).py|Command prompt input|
|:------------------------|:-------------------|
|Usage: Type a single option then press enter type filename press enter again for help type help for the turtle type ban!|Screen output       |
|Enter option: pfx        |Screen output       |
|Enter filename: filename.txt|Screen output       |
|Payload is being generated please wait...|Screen output       |
|Payload mutation is finished enjoy...|Screen output       |
|Now fuzz an application...|Screen output       |


**Note:**The tool after properly executed will generate a file named after the mutation type and the current time so you can track the generated payloads, the file name would look like this Tue04Sep18\_15\_11\_postfixedPayloads.lst.

**Example 3:** Script execution for generating all SQL injection mutations:

|root# ./tmntv(version).py|Command prompt input|
|:------------------------|:-------------------|
|Usage: Type a single option then press enter type filename press enter again for help type help for the turtle type ban!|Screen output       |
|Enter option: all        |Screen output       |
|Enter filename: filename.txt|Screen output       |
|Payload is being generated please wait...|Screen output       |
|----- Payload generated ----|Screen output       |
|----- Payload generated ----|Screen output       |
|----- Payload generated ----|Screen output       |
|----- Payload generated ----|Screen output       |
|----- Payload generated ----|Screen output       |
|----- Payload generated ----|Screen output       |
|----- Payload generated ----|Screen output       |
|Payload mutation is finished enjoy...|Screen output       |
|Now fuzz an application...|Screen output       |


**Note:**The tool after properly executed will generate a seven files named after the mutation type and the current time so you can track the generated payloads, the file names would look like this:

  1. Tue04Sep18\_15\_11\_postfixedPayloads.lst
  1. Tue04Sep18\_15\_16\_suffixedPayloads.lst
  1. Tue04Sep18\_15\_21\_spaceFilledPayloads.lst
  1. Tue04Sep18\_15\_26\_urlEncodedPayloads.lst