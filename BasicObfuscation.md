
---

# Bypassing SQL Injection filters #

---


The are numerous ways to by pass SQL injection filters, there even more ways to exploit them too. The most common way of evading SQL injection filters are:

  1. Using Case Variation
  1. Using SQL Comments
  1. Using URL Encoding
  1. Using Dynamic Query Execution
  1. Using Null Bytes

Take notice that all the above SQL injection filter bypassing techniques are based on the black list filtering mentality, and not the white list filtering logic. This means that bad software development is based in black list filters concept.

## Using Case Variation ##

If a keyword-blocking filter is particularly naive, you may be able to circumvent it by varying the case of the characters in your attack string, because the database handles SQL keywords in a case-insensitive manner.

For example, if the following input is being blocked:

' UNION SELECT @@version --

You may be able to bypass the filter using the following alternative:

' UnIoN sElEcT @@version --

**Note**: Using only uppercase or only lower case might also work, but I would not suggest spending a lot of time in that type of fuzzing.

## Using SQL Comments ##

You can use in-line comment sequences to create snippets of SQL which are syntactically unusual but perfectly valid, and which bypass various kinds of input filters. You can circumvent various simple pattern-matching filters in this way.

Of course, you can use this same technique to bypass filters which simply block any white-space whatsoever. Many developers wrongly believe that by restricting input to a single token they are preventing SQL injection attacks, forgetting that in-line comments enable an attacker to construct arbitrarily complex SQL without using any spaces.

In the case of MySQL, you can even use in-line comments within SQL keywords, enabling many common keyword-blocking filters to be circumvented. For example, the following attack will still work if the back-end database is MySQL if you only check for spaces to SQL injection strings:

' UNION//SELECT//@@version//-- Or ' U//NI//ON//SELECT//@@version//--

**Note**: This type of filter bypassing methodology covers gap filling and black list bad character sequence filtering.

## Using URL Encoding ##

URL encoding is a versatile technique that you can use to defeat many kinds of input filters. In its most basic form, this involves replacing problematic characters with their ASCII code in hexadecimal form, preceded by the % character. For example, the ASCII code for a single quotation mark is 0x27, so its URL-encoded representation is %27.In this situation, you can use an attack such as the following to bypass a filter:

Original query:

' UNION SELECT @@version --

URL-encoded query:

%27%20%55%4e%49%4f%4e%20%53%45%4c%45%43%54%20%40%40%76%65%72%73%69%6f%6e%20%2d%2d

In other cases, this basic URL-encoding attack does not work, but you can nevertheless circumvent the filter by double-URL-encoding the blocked characters. In the double encoded attack, the % character in the original attack is itself URL-encoded in the normal way (as %25) so that the double-URL-encoded form of a single quotation mark is %2527.If you modify the preceding attack to use double-URL encoding, it looks like this:

%25%32%37%25%32%30%25%35%35%25%34%65%25%34%39%25%34%66%25%34%65%25
%32%30%25%35%33%25%34%35%25%34%63%25%34%35%25%34%33%25%35%34%25%32
%30%25%34%30%25%34%30%25%37%36%25%36%35%25%37%32%25%37%33%25%36%39
%25%36%66%25%36%65%25%32%30%25%32%64%25%32%64

**Note**: You should also take into consideration that selective URL-encoding is also a valid way to by pass SQL injection filtering.

Double-URL encoding sometimes works because Web applications sometimes decode user input more than once, and apply their input filters before the final decoding step. In the preceding example, the steps involved are as follows:

  * The attacker supplies the input ‘%252f%252a∗/UNION …
  * The application URL decodes the input as ‘%2f%2a∗/ UNION…
  * The application validates that the input does not contain /∗ (which it doesn't).
  * The application URL decodes the input as ‘/∗∗/ UNION…
  * The application processes the input within an SQL query, and the attack is successful.

A further variation on the URL-encoding technique is to use Unicode encodes of blocked characters. As well as using the % character with a two-digit hexadecimal ASCII code, URL encoding can employ various Unicode representations of characters. There is a very good web site you can use to test various  types of encoding here. The SQL Injection query when unicode encoded looks like this:

27 20 55 4E 49 4F 4E 20 53 45 4C 45 43 54 20 40 40 76 65 72 73 69 6F 6E 20 2D 2D

**Note**: I have not been experimenting a lot with unicode encoding and frankly I do not think it is going to be very useful fuzzing SQL with Unicode encoding.

Further, because of the complexity of the Unicode specification, decoders often tolerate illegal encoding and decode them on a “closest fit” basis. If an application's input validation checks for certain literal and Unicode-encoded strings, it may be possible to submit illegal encoding of blocked characters, which will be accepted by the input filter but which will decode appropriately to deliver a successful attack.

## Using Dynamic Query Execution ##

Many databases allow SQL queries to be executed dynamically, by passing a string containing an SQL query into a database function which executes the query. If you have discovered a valid SQL injection point, but find that the application’s input filters are blocking queries you want to inject, you may be able to use dynamic execution to circumvent the filters. Dynamic query execution works differently on different databases.

On Microsoft SQL Server, you can use the EXEC function to execute a query in string form. For example:

'EXEC xp\_cmdshell ‘dir’; — Or 'UNION EXEC xp\_cmdshell ‘dir’; —

**Note**: Using the EXEC function you can enumerate all enabled stored procedures in the back end database and also map assigned privileges to stored procedures.

In Oracle, you can use the EXECUTE IMMEDIATE command to execute a query in string form. For example:

DECLARE pw VARCHAR2(1000);
BEGIN
> EXECUTE IMMEDIATE 'SELECT password FROM tblUsers' INTO pw;
> DBMS\_OUTPUT.PUT\_LINE(pw);
END;

**Note**:  You can do that line by line or all together, of course other filter by passing methodologies can be combined with this one.

The above SQL injection attack type can be submitted to the web application attack entry point, either the way it is presented above or as a batch of commands separated by semicolons when the back end database accepts batch queries (e.g. MSSQL).

For example in MSSQL you could do something like this:

SET @MSSQLVERSION  = SELECT @@VERSION; EXEC (@MSSQLVERSION); --

**Note**: The same query can be submitted from different web application entry points or the same.

Databases provide various means of manipulating strings, and the key to using dynamic execution to defeat input filters is to use the string manipulation functions to convert input that is allowed by the filters into a string which contains your desired query. In the simplest case, you can use string concatenation to construct a string from smaller parts. Different databases use different syntax for string concatenation.

For example, if the SQL keyword SELECT is blocked, you can construct it as follows:

  1. Oracle: 'SEL'||'ECT'
  1. MS-SQL: 'SEL'+'ECT'
  1. MySQL: 'SEL' 'ECT'

Note that SQL Server uses a + character for concatenation, whereas MySQL uses a space. If you are submitting these characters in an HTTP request, you will need to URLencode them as %2b and %20, respectively. Going further, you can construct individual characters using the CHAR function (CHR in Oracle) using their ASCII character code. For example, to construct the SELECT keyword on SQL Server, you can use:

CHAR(83)+CHAR(69)+CHAR(76)+CHAR(69)+CHAR(67)+CHAR(84)

**Note**: The Firefox plug-in tool called Hackbar is also doing that automatically (for a long time now).

You can construct strings in this way without using any quotation mark characters. If you have an SQL injection entry point where quotation marks are blocked, you can use the CHAR function to place strings (such as ‘admin’) into your exploits. Other string manipulation functions may be useful as well. For example, Oracle includes the functions REVERSE, TRANSLATE, REPLACE, and SUBSTR. Another way to construct strings for dynamic execution on the SQL Server platform is to instantiate a string from a single hexadecimal number which represents the string’s ASCII character codes. For example, the string:

SELECT password FROM tblUsers

Can be constructed and dynamically executed as follows:

DECLARE @query VARCHAR(100)
> SELECT @query = 0x53454c4543542070617373776f72642046524f4d2074626c5573657273
EXEC(@query)

**Note**: The mass SQL injection attacks against Web applications that started in early 2008 employed this technique to reduce the chance of their exploit code being blocked by input filters in the applications being attacked.

## Using Null Bytes ##

Often, the input filters which you need to bypass in order to exploit an SQL injection vulnerability are implemented outside the application's own code, in intrusion detection systems (IDSs) or WAFs. For performance reasons, these components are typically written in native code languages, such as C++. In this situation, you can often use null byte attacks to circumvent input filters and smuggle your exploits into the back-end application.

Null byte attacks work due to the different ways that null bytes are handled in native and managed code. In native code, the length of a string is determined by the position of the first null byte from the start of the string—the null byte effectively terminates the string. In managed code, on the other hand, string objects comprise a character array (which may contain null bytes) and a separate record of the string's length. This difference means that when the native filter processes your input, it may stop processing the input when it encounters a null byte, because this denotes the end of the string as far as the filter is concerned. If the input prior to the null byte is benign, the filter will not block the input.

However, when the same input is processed by the application, in a managed code context, the full input following the null byte will be processed, allowing your exploit to be executed. To perform a null byte attack, you simply need to supply a URL-encoded null byte (that looks like this ) prior to any characters that the filter is blocking. In the original example, you may be able to circumvent native input filters using an attack string such as the following:

' UNION SELECT password FROM tblUsers WHERE username='admin'--

**Note**: When access is used as a back end database NULL bytes can be used as SQL query delimiter.