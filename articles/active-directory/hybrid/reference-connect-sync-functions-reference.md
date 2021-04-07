---
title: 'Azure AD Connect Sync: functions-hivatkozás | Microsoft Docs'
description: A deklaratív kiépítési kifejezések hivatkozása Azure AD Connect-szinkronizálásban.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4f525ca0-be0e-4a2e-8da1-09b6b567ed5f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 07/12/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7454733233f1fd487d774d52a6f46187354ae05c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246706"
---
# <a name="azure-ad-connect-sync-functions-reference"></a>Azure AD Connect Sync: függvények referenciája
Azure AD Connect a függvények egy attribútumérték manipulálására szolgálnak a szinkronizálás során.  
A függvények szintaxisa a következő formátumban van kifejezve:  
`<output type> FunctionName(<input type> <position name>, ..)`

Ha egy függvény túlterhelt, és több szintaxist is elfogad, az összes érvényes szintaxis megjelenik.  
A függvények erősen beírhatók, és ellenőrzik, hogy az átadott típus megegyezik-e a dokumentált típussal.  
Ha a típus nem egyezik, a rendszer hibát jelez.

A típusokat a következő szintaxissal fejezzük ki:

* **bin** – bináris
* **bool** – Boolean
* **DT** – UTC dátum/idő
* **Enum** – ismert konstansok enumerálása
* **exp** – kifejezés, amely várhatóan logikai értékre lesz kiértékelve
* **mvbin** – többértékű bináris
* **mvstr** – többértékű sztring
* **mvref** – többértékű hivatkozás
* **NUM** – numerikus
* **ref** – hivatkozás
* **Str** – string
* **var** – a (szinte) bármely más típusú változata
* **Void** – nem ad vissza értéket

A **mvbin**, **mvstr** és **mvref** típusú függvények csak többértékű attribútumokon működhetnek. A **bin**, az **Str** és a **ref** függvények az egyértékű és a többértékű attribútumok esetében is működnek.

## <a name="functions-reference"></a>Functions – referencia

* **Tanúsítvány**
  * [CertExtensionOids](#certextensionoids)
  * [CertFormat](#certformat)
  * [CertFriendlyName](#certfriendlyname)
  * [CertHashString](#certhashstring)
  * [CertIssuer](#certissuer)
  * [CertIssuerDN](#certissuerdn)
  * [CertIssuerOid](#certissueroid)
  * [CertKeyAlgorithm](#certkeyalgorithm)
  * [CertKeyAlgorithmParams](#certkeyalgorithmparams)
  * [CertNameInfo](#certnameinfo)
  * [CertNotAfter](#certnotafter)
  * [CertNotBefore](#certnotbefore)
  * [CertPublicKeyOid](#certpublickeyoid)
  * [CertPublicKeyParametersOid](#certpublickeyparametersoid)
  * [CertSerialNumber](#certserialnumber)
  * [CertSignatureAlgorithmOid](#certsignaturealgorithmoid)
  * [CertSubject](#certsubject)
  * [CertSubjectNameDN](#certsubjectnamedn)
  * [CertSubjectNameOid](#certsubjectnameoid)
  * [CertThumbprint](#certthumbprint)
  * [CertVersion](#certversion)
  * [IsCert](#iscert)
* **Átalakítás**
  * [CBool](#cbool)
  * [CDate](#cdate)
  * [CGuid](#cguid)
  * [ConvertFromBase64](#convertfrombase64)
  * [ConvertToBase64](#converttobase64)
  * [ConvertFromUTF8Hex](#convertfromutf8hex)
  * [ConvertToUTF8Hex](#converttoutf8hex)
  * [CNum](#cnum)
  * [CRef](#cref)
  * [CStr](#cstr)
  * [StringFromGuid](#stringfromguid)
  * [StringFromSid](#stringfromsid)
* **Dátum és idő**
  * [DateAdd](#dateadd)
  * [DateFromNum](#datefromnum)
  * [FormatDateTime](#formatdatetime)
  * [Most](#now)
  * [NumFromDate](#numfromdate)
* **Címtár**
  * [DNComponent](#dncomponent)
  * [DNComponentRev](#dncomponentrev)
  * [EscapeDNComponent](#escapedncomponent)
* **Értékelés**
  * [IsBitSet](#isbitset)
  * [IsDate](#isdate)
  * [IsEmpty](#isempty)
  * [IsGuid](#isguid)
  * [IsNull](#isnull)
  * [IsNullOrEmpty](#isnullorempty)
  * [IsNumeric](#isnumeric)
  * [IsPresent](#ispresent)
  * [IsString](#isstring)
* **Matematikai**
  * [BitAnd](#bitand)
  * [BitOr](#bitor)
  * [RandomNum](#randomnum)
* **Többszörös * érték**
  * [Tartalmaz](#contains)
  * [Száma](#count)
  * [Elem](#item)
  * [ItemOrNull](#itemornull)
  * [Csatlakozás](#join)
  * [RemoveDuplicates](#removeduplicates)
  * [Felosztása](#split)
* **Program folyamata**
  * [Hiba](#error)
  * [IIF](#iif)
  * [Kiválasztás](#select)
  * [Kapcsoló (switch)](#switch)
  * [Ahol](#where)
  * [A](#with)
* **Szöveg**
  * [GUID](#guid)
  * [InStr](#instr)
  * [InStrRev](#instrrev)
  * [LCase](#lcase)
  * [Bal](#left)
  * [Len](#len)
  * [LTrim](#ltrim)
  * [Közepes](#mid)
  * [PadLeft](#padleft)
  * [PadRight](#padright)
  * [PCase](#pcase)
  * [Csere](#replace)
  * [ReplaceChars](#replacechars)
  * [Jobb gombbal](#right)
  * [RTrim](#rtrim)
  * [Trim](#trim)
  * [UCase](#ucase)
  * [Word](#word)

---
### <a name="bitand"></a>BitAnd
**Leírás:**  
A BitAnd függvény a megadott biteket egy értékre állítja be.

**Szintaxis**  
`num BitAnd(num value1, num value2)`

* érték1, érték2: numerikus értékek, amelyeket össze kell AND'ed

**Megjegyzéseket tartalmazó**  
Ez a függvény mindkét paramétert a bináris ábrázolásra konvertálja, és egy kis mennyiséget állít be a következőre:

* 0 – Ha a *érték1* -ben és a *érték2* -ben található megfelelő bitek közül egyet vagy mindkettőt 0
* 1 – Ha a megfelelő bitek mindegyike 1.

Más szóval a 0 értéket adja vissza minden esetben, kivéve, ha mindkét paraméternek megfelelő bitek értéke 1.

**Példa**  
`BitAnd(&HF, &HF7)`  
A 7 értéket adja vissza, mert a hexadecimális "F" és "F7" kifejezés kiértékelése erre az értékre történik.

---
### <a name="bitor"></a>BitOr
**Leírás:**  
A BitOr függvény a megadott biteket egy értékre állítja be.

**Szintaxis**  
`num BitOr(num value1, num value2)`

* érték1, érték2: numerikus értékek, amelyeket össze kell OR'ed

**Megjegyzéseket tartalmazó**  
Ez a függvény mindkét paramétert a bináris ábrázolásra konvertálja, és egy kis értéket állít be, ha a maszkban és a jelölőben lévő megfelelő bitek közül egy vagy mindkettő 1, illetve 0, ha a megfelelő bitek közül mindkettő 0. Ez azt jelenti, hogy minden esetben 1 értéket ad vissza, kivéve, ha mindkét paraméter megfelelő bitje 0.

---
### <a name="cbool"></a>CBool
**Leírás:**  
A CBool függvény egy logikai értéket ad vissza a kiértékelt kifejezés alapján.

**Szintaxis**  
`bool CBool(exp Expression)`

**Megjegyzéseket tartalmazó**  
Ha a kifejezés kiértékelése nem nulla értékű, akkor a CBool igaz értéket ad vissza, máskülönben hamis értéket ad vissza.

**Példa**  
`CBool([attrib1] = [attrib2])`  

Igaz értéket ad vissza, ha mindkét attribútum ugyanazzal az értékkel rendelkezik.

---
### <a name="cdate"></a>CDate
**Leírás:**  
A CDate függvény egy UTC DateTime értéket ad vissza egy karakterláncból. A DateTime nem egy natív attribútum-típus a szinkronizálásban, de egyes függvények használják.

**Szintaxis**  
`dt CDate(str value)`

* Érték: dátummal, idővel és opcionálisan időzónával rendelkező sztring

**Megjegyzéseket tartalmazó**  
A visszaadott karakterlánc mindig UTC-ben van.

**Példa**  
`CDate([employeeStartTime])`  
Egy DateTime értéket ad vissza az alkalmazott kezdési időpontja alapján

`CDate("2013-01-10 4:00 PM -8")`  
Egy "2013-01-11 12:00 AM" értéket jelölő DateTime értéket ad vissza.


---
### <a name="certextensionoids"></a>CertExtensionOids
**Leírás:**  
Egy tanúsítványfájl kritikus bővítményeinek OID-értékeit adja vissza.

**Szintaxis**  
`mvstr CertExtensionOids(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certformat"></a>CertFormat
**Leírás:**  
Az X. 509v3 tanúsítvány formátumának nevét adja vissza.

**Szintaxis**  
`str CertFormat(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certfriendlyname"></a>CertFriendlyName
**Leírás:**  
Egy tanúsítvány társított aliasát adja vissza.

**Szintaxis**  
`str CertFriendlyName(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certhashstring"></a>CertHashString
**Leírás:**  
Az X. 509v3 tanúsítvány SHA1-kivonatának értékét adja vissza hexadecimális karakterláncként.

**Szintaxis**  
`str CertHashString(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certissuer"></a>CertIssuer
**Leírás:**  
Az X. 509v3 tanúsítványt kiállító hitelesítésszolgáltató nevét adja vissza.

**Szintaxis**  
`str CertIssuer(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certissuerdn"></a>CertIssuerDN
**Leírás:**  
A tanúsítvány kiállítójának megkülönböztető nevét adja vissza.

**Szintaxis**  
`str CertIssuerDN(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certissueroid"></a>CertIssuerOid
**Leírás:**  
A tanúsítvány kiállítójának objektumazonosítót adja vissza.

**Szintaxis**  
`str CertIssuerOid(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certkeyalgorithm"></a>CertKeyAlgorithm
**Leírás:**  
Az X. 509v3 tanúsítvány kulcs-algoritmusának adatait adja vissza karakterláncként.

**Szintaxis**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certkeyalgorithmparams"></a>CertKeyAlgorithmParams
**Leírás:**  
Az X. 509v3 tanúsítvány kulcs-algoritmusának paramétereit adja vissza hexadecimális karakterláncként.

**Szintaxis**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certnameinfo"></a>CertNameInfo
**Leírás:**  
Egy tanúsítvány tulajdonosi és kiállítói nevét adja vissza.

**Szintaxis**  
`str CertNameInfo(binary certificateRawData, str x509NameType, bool includesIssuerName)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.
*   X509NameType: a tulajdonos X509NameType értéke.
*   includesIssuerName: true (igaz) – a kiállító nevének belefoglalása; Ellenkező esetben hamis.

---
### <a name="certnotafter"></a>CertNotAfter
**Leírás:**  
Azt a helyi időpontot adja vissza, amely után a tanúsítvány már nem érvényes.

**Szintaxis**  
`dt CertNotAfter(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certnotbefore"></a>CertNotBefore
**Leírás:**  
A helyi idő szerinti dátumot adja vissza, amikor a tanúsítvány érvényes lesz.

**Szintaxis**  
`dt CertNotBefore(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certpublickeyoid"></a>CertPublicKeyOid
**Leírás:**  
Az X. 509v3 tanúsítvány nyilvános kulcsának OID-azonosítóját adja vissza.

**Szintaxis**  
`str CertKeyAlgorithm(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certpublickeyparametersoid"></a>CertPublicKeyParametersOid
**Leírás:**  
Az X. 509v3 tanúsítványhoz tartozó nyilvános kulcsú paraméterek OID azonosítóját adja vissza.

**Szintaxis**  
`str CertPublicKeyParametersOid(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certserialnumber"></a>CertSerialNumber
**Leírás:**  
Az X. 509v3 tanúsítvány sorozatszámát adja vissza.

**Szintaxis**  
`str CertSerialNumber(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certsignaturealgorithmoid"></a>CertSignatureAlgorithmOid
**Leírás:**  
A tanúsítvány aláírásának létrehozásához használt algoritmus OID-azonosítóját adja vissza.

**Szintaxis**  
`str CertSignatureAlgorithmOid(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certsubject"></a>CertSubject
**Leírás:**  
Lekéri a tulajdonos megkülönböztető nevét egy tanúsítványból.

**Szintaxis**  
`str CertSubject(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certsubjectnamedn"></a>CertSubjectNameDN
**Leírás:**  
A tulajdonos megkülönböztető nevét adja vissza egy tanúsítványból.

**Szintaxis**  
`str CertSubjectNameDN(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certsubjectnameoid"></a>CertSubjectNameOid
**Leírás:**  
A tulajdonos nevének OID-azonosítóját adja vissza egy tanúsítványból.

**Szintaxis**  
`str CertSubjectNameOid(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certthumbprint"></a>CertThumbprint
**Leírás:**  
Egy tanúsítvány ujjlenyomatát adja vissza.

**Szintaxis**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="certversion"></a>CertVersion
**Leírás:**  
Egy tanúsítvány X. 509 formátumú verzióját adja vissza.

**Szintaxis**  
`str CertThumbprint(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.

---
### <a name="cguid"></a>CGuid
**Leírás:**  
A CGuid függvény egy GUID karakterlánc-ábrázolását átalakítja bináris ábrázolásra.

**Szintaxis**  
`bin CGuid(str GUID)`

* Az ebben a mintában formázott karakterlánc: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX vagy {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}

---
### <a name="contains"></a>Contains
**Leírás:**  
A tartalmaz függvény megkeres egy karakterláncot egy többértékű attribútumon belül

**Szintaxis**  
`num Contains (mvstring attribute, str search)` – kis-és nagybetűk megkülönböztetése  
`num Contains (mvstring attribute, str search, enum Casetype)`  
`num Contains (mvref attribute, str search)` – kis-és nagybetűk megkülönböztetése

* attribútum: a keresendő többértékű attribútum.
* Keresés: az attribútumban keresendő karakterlánc.
* Casetype: CaseInsensitive vagy CaseSensitive.

A többértékű attribútum azon indexét adja vissza, amelyben a sztring található. a 0 értéket adja vissza, ha a sztring nem található.

**Megjegyzéseket tartalmazó**  
A Többértékű karakterlánc-attribútumok esetében a keresés alsztringeket keres az értékekben.  
A hivatkozási attribútumok esetében a keresett karakterláncnak pontosan egyeznie kell az egyezőnek tekintendő értékkel.

**Példa**  
`IIF(Contains([proxyAddresses],"SMTP:")>0,[proxyAddresses],Error("No primary SMTP address found."))`  
Ha a proxyAddresses attribútum elsődleges e-mail-címmel rendelkezik (amelyet a nagybetűs "SMTP:" kifejezés jelöl), akkor adja vissza a proxyAddress attribútumot, máskülönben hibát ad vissza.

---
### <a name="convertfrombase64"></a>ConvertFromBase64
**Leírás:**  
A ConvertFromBase64 függvény a megadott Base64 kódolású értéket egy normál karakterlánccá alakítja át.

**Szintaxis**  
`str ConvertFromBase64(str source)` -Unicode kódolást feltételez  
`str ConvertFromBase64(str source, enum Encoding)`

* Forrás: Base64 kódolású karakterlánc  
* Kódolás: Unicode, ASCII, UTF8

**Példa**  
`ConvertFromBase64("SABlAGwAbABvACAAdwBvAHIAbABkACEA")`  
`ConvertFromBase64("SGVsbG8gd29ybGQh", UTF8)`

Mindkét példa "*Helló világ!*" értéket ad vissza.

---
### <a name="convertfromutf8hex"></a>ConvertFromUTF8Hex
**Leírás:**  
A ConvertFromUTF8Hex függvény a megadott UTF8 hexadecimális kódolású értéket karakterlánccá alakítja át.

**Szintaxis**  
`str ConvertFromUTF8Hex(str source)`

* Forrás: UTF8 2 bájtos kódolású Sting

**Megjegyzéseket tartalmazó**  
A függvény és a ConvertFromBase64 ([], UTF8) közötti különbség abban az esetben, ha az eredmény a DN attribútumhoz tartozó felhasználóbarát.  
Ezt a formátumot a Azure Active Directory használja DN-ként.

**Példa**  
`ConvertFromUTF8Hex("48656C6C6F20776F726C6421")`  
A "*Helló világ!*" értéket adja vissza.

---
### <a name="converttobase64"></a>ConvertToBase64
**Leírás:**  
A ConvertToBase64 függvény egy karakterláncot Unicode Base64 karakterlánccá alakít át.  
Egész számokból álló tömb értékét konvertálja az egyenértékű karakterlánc-ábrázolásra, amely Base-64 számjegyekkel van kódolva.

**Szintaxis**  
`str ConvertToBase64(str source)`

**Példa**  
`ConvertToBase64("Hello world!")`  
A "SABlAGwAbABvACAAdwBvAHIAbABkACEA" értéket adja vissza

---
### <a name="converttoutf8hex"></a>ConvertToUTF8Hex
**Leírás:**  
A ConvertToUTF8Hex függvény egy karakterláncot UTF8 hexadecimális kódolású értékre alakít át.

**Szintaxis**  
`str ConvertToUTF8Hex(str source)`

**Megjegyzéseket tartalmazó**  
A függvény kimeneti formátumát a Azure Active Directory használja DN-attribútum formátumként.

**Példa**  
`ConvertToUTF8Hex("Hello world!")`  
48656C6C6F20776F726C6421 visszaadása

---
### <a name="count"></a>Darabszám
**Leírás:**  
A Count függvény egy többértékű attribútum elemeinek számát adja vissza.

**Szintaxis**  
`num Count(mvstr attribute)`

---
### <a name="cnum"></a>CNum
**Leírás:**  
A CNum függvény egy karakterláncot vesz fel, és numerikus adattípust ad vissza.

**Szintaxis**  
`num CNum(str value)`

---
### <a name="cref"></a>CRef
**Leírás:**  
Sztring átalakítása hivatkozási attribútumra

**Szintaxis**  
`ref CRef(str value)`

**Példa**  
`CRef("CN=LC Services,CN=Microsoft,CN=lcspool01,CN=Pools,CN=RTC Service," & %Forest.LDAP%)`

---
### <a name="cstr"></a>CStr
**Leírás:**  
A CStr függvény karakterlánc típusú adattípusra konvertál.

**Szintaxis**  
`str CStr(num value)`  
`str CStr(ref value)`  
`str CStr(bool value)`  

* Value: numerikus érték, hivatkozási attribútum vagy logikai érték lehet.

**Példa**  
`CStr([dn])`  
Visszatérhet a "CN = Joe, DC = contoso, DC = com" értékre.

---
### <a name="dateadd"></a>DateAdd
**Leírás:**  
Egy olyan dátumot ad vissza, amely egy megadott időintervallum hozzáadására szolgáló dátumot tartalmaz.

**Szintaxis**  
`dt DateAdd(str interval, num value, dt date)`

* intervallum: karakterlánc-kifejezés, amely a hozzáadni kívánt időintervallum. A karakterláncnak a következő értékek egyikével kell rendelkeznie:
  * éééé év
  * q negyedév
  * m hónap
  * év napja
  * d nap
  * w hétköznap
  * WW hét
  * h óra
  * n perc
  * s másodperc
* érték: a felvenni kívánt egységek száma. Ez lehet pozitív (a jövőbeli dátumok beolvasása) vagy negatív (a múltbeli dátumok beszerzéséhez).
* Date: dátum és idő, amely az intervallum hozzáadásának dátumát jelképezi.

**Példa**  
`DateAdd("m", 3, CDate("2001-01-01"))`  
3 hónapot ad vissza, és egy "2001-04-01" értéket jelölő DateTime értéket ad vissza.

---
### <a name="datefromnum"></a>DateFromNum
**Leírás:**  
A DateFromNum függvény egy értéket AD meg az AD dátumformátum dátum és idő típusára.

**Szintaxis**  
`dt DateFromNum(num value)`

**Példa**  
`DateFromNum([lastLogonTimestamp])`  
`DateFromNum(129699324000000000)`  
A 2012-01-01 23:00:00-et jelölő DateTime értéket ad vissza.

---
### <a name="dncomponent"></a>DNComponent
**Leírás:**  
A DNComponent függvény a megadott DN-összetevő értékét adja vissza balról.

**Szintaxis**  
`str DNComponent(ref dn, num ComponentNumber)`

* DN: az értelmezni kívánt hivatkozási attribútum
* ComponentNumber: a DN által visszaadott összetevő

**Példa**  
`DNComponent(CRef([dn]),1)`  
Ha a DN a következő: "CN = Joe, ou =...", visszaadja a következőt: Joe

---
### <a name="dncomponentrev"></a>DNComponentRev
**Leírás:**  
A DNComponentRev függvény egy megadott DN-összetevő értékét adja vissza, amely jobbról (a végéről) fog származni.

**Szintaxis**  
`str DNComponentRev(ref dn, num ComponentNumber)`  
`str DNComponentRev(ref dn, num ComponentNumber, enum Options)`

* DN: az értelmezni kívánt hivatkozási attribútum
* ComponentNumber – a DN által visszaadott összetevő
* Beállítások: DC – az összes összetevő figyelmen kívül hagyása a "DC =" értékkel

**Példa**  
Ha a DN a "CN = Joe, OU = Atlanta, OU = GA, OU = US, DC = contoso, DC = com", akkor  
`DNComponentRev(CRef([dn]),3)`  
`DNComponentRev(CRef([dn]),1,"DC")`  
Mindkettő visszatér hozzánk.

---
### <a name="error"></a>Hiba
**Leírás:**  
A Error függvény egyéni hiba visszaküldésére szolgál.

**Szintaxis**  
`void Error(str ErrorMessage)`

**Példa**  
`IIF(IsPresent([accountName]),[accountName],Error("AccountName is required"))`  
Ha a accountName attribútum nem található, hibát jelez az objektumon.

---
### <a name="escapedncomponent"></a>EscapeDNComponent
**Leírás:**  
A EscapeDNComponent függvény a DN egyik összetevőjét veszi át, és megmenekül, hogy az LDAP-ban is megjelenjen.

**Szintaxis**  
`str EscapeDNComponent(str value)`

**Példa**  
`EscapeDNComponent("cn=" & [displayName]) & "," & %ForestLDAP%)`  
Gondoskodik arról, hogy az objektum az LDAP-címtárban is létrehozható legyen, még akkor is, ha a displayName attribútum olyan karaktereket tartalmaz, amelyeket az LDAP-ban el kell menekülni.

---
### <a name="formatdatetime"></a>FormatDateTime
**Leírás:**  
A FormatDateTime függvény egy dátum és idő formázása megadott formátumú karakterláncra

**Szintaxis**  
`str FormatDateTime(dt value, str format)`

* Value: egy dátum és idő formátumú érték
* Format (formátum): a konvertálandó formátumot jelképező karakterlánc.

**Megjegyzéseket tartalmazó**  
A formátum lehetséges értékei itt találhatók: [Egyéni dátum-és időformátumok a FORMAT függvényhez](/dax/custom-date-and-time-formats-for-the-format-function).

**Példa**  

`FormatDateTime(CDate("12/25/2007"),"yyyy-MM-dd")`  
Eredmények: "2007-12-25".

`FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")`  
A "20140905081453.0 Z" kifejezéssel járhat

---
### <a name="guid"></a>Guid
**Leírás:**  
A függvény GUID-azonosítója új véletlenszerű GUID azonosítót hoz létre

**Szintaxis**  
`str Guid()`

---
### <a name="iif"></a>IIF
**Leírás:**  
Az IIF függvény a lehetséges értékek egy halmazát adja vissza egy megadott feltétel alapján.

**Szintaxis**  
`var IIF(exp condition, var valueIfTrue, var valueIfFalse)`

* feltétel: bármely olyan érték vagy kifejezés, amelynek kiértékelése igaz vagy hamis lehet.
* valueIfTrue: Ha a feltétel igaz értéket ad vissza, a visszaadott érték.
* valueIfFalse: Ha a feltétel hamis értéket ad vissza, a visszaadott érték.

**Példa**  
`IIF([employeeType]="Intern","t-" & [alias],[alias])`  
 Ha a felhasználó egy gyakornok, a a "t-" értékkel rendelkező felhasználó aliasát adja vissza, a másik pedig a felhasználó aliasát adja vissza.

---
### <a name="instr"></a>InStr
**Leírás:**  
A beosztási függvény megkeresi egy karakterláncban szereplő alsztring első előfordulását.

**Szintaxis**  

`num InStr(str stringcheck, str stringmatch)`  
`num InStr(str stringcheck, str stringmatch, num start)`  
`num InStr(str stringcheck, str stringmatch, num start, enum compare)`

* karakterlánc: keresendő karakterlánc
* keresettkarakterlánc: a rendszer megtalálni kívánt karakterláncot
* Kezdés: a pozíció megkeresése az alsztring megtalálásához
* összehasonlítás: vbTextCompare vagy vbBinaryCompare

**Megjegyzéseket tartalmazó**  
Azt a pozíciót adja vissza, ahol az alkarakterlánc található vagy 0, ha nem található.

**Példa**  
`InStr("The quick brown fox","quick")`  
Evalues – 5

`InStr("repEated","e",3,vbBinaryCompare)`  
Értékelés 7-re

---
### <a name="instrrev"></a>InStrRev
**Leírás:**  
A InStrRev függvény megkeresi egy karakterlánc utolsó előfordulását egy sztringben.

**Szintaxis**  
`num InstrRev(str stringcheck, str stringmatch)`  
`num InstrRev(str stringcheck, str stringmatch, num start)`  
`num InstrRev(str stringcheck, str stringmatch, num start, enum compare)`

* karakterlánc: keresendő karakterlánc
* keresettkarakterlánc: a rendszer megtalálni kívánt karakterláncot
* Kezdés: a pozíció megkeresése az alsztring megtalálásához
* összehasonlítás: vbTextCompare vagy vbBinaryCompare

**Megjegyzéseket tartalmazó**  
Azt a pozíciót adja vissza, ahol az alkarakterlánc található vagy 0, ha nem található.

**Példa**  
`InStrRev("abbcdbbbef","bb")`  
7 értéket ad vissza.

---
### <a name="isbitset"></a>IsBitSet
**Leírás:**  
A függvény IsBitSet teszteket végez, ha egy bit be van állítva.

**Szintaxis**  
`bool IsBitSet(num value, num flag)`

* Value (érték): a kiértékelt numerikus érték. jelző: egy numerikus érték, amelynek a kiértékelése megtörtént.

**Példa**  
`IsBitSet(&HF,4)`  
Igaz értéket ad vissza, mert a "4" érték be van állítva az "F" hexadecimális értékben.

---
### <a name="isdate"></a>IsDate
**Leírás:**  
Ha a kifejezés DateTime típusúként is kiértékelhető, akkor a IsDate függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsDate(var Expression)`

**Megjegyzéseket tartalmazó**  
Annak megállapítására szolgál, hogy a CDate () sikeres lehet-e.

---
### <a name="iscert"></a>IsCert
**Leírás:**  
Igaz értéket ad vissza, ha a nyers adatok a .NET X509certificate2) tanúsítvány objektumba szerializálható.

**Szintaxis**  
`bool CertThumbprint(binary certificateRawData)`  
*   certificateRawData: egy X. 509 tanúsítvány byte Array ábrázolása. A bájtos tömb lehet bináris (DER) kódolású vagy Base64 kódolású X. 509 típusú.
---
### <a name="isempty"></a>IsEmpty
**Leírás:**  
Ha az attribútum szerepel a CS-ban vagy az MV-ban, de egy üres karakterláncot ad vissza, akkor a IsEmpty függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsEmpty(var Expression)`

---
### <a name="isguid"></a>IsGuid
**Leírás:**  
Ha a karakterlánc konvertálható GUID-ra, akkor a IsGuid függvény értéke TRUE (igaz).

**Szintaxis**  
`bool IsGuid(str GUID)`

**Megjegyzéseket tartalmazó**  
A GUID a következő minták egyikét követő karakterláncként van definiálva: XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX vagy {XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX}

Annak megállapítására szolgál, hogy a CGuid () sikeres lehet-e.

**Példa**  
`IIF(IsGuid([strAttribute]),CGuid([strAttribute]),NULL)`  
Ha a StrAttribute GUID formátumú, a bináris ábrázolást ad vissza, ellenkező esetben null értéket ad vissza.

---
### <a name="isnull"></a>IsNull
**Leírás:**  
Ha a kifejezés értéke null, akkor a IsNull függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsNull(var Expression)`

**Megjegyzéseket tartalmazó**  
Attribútum esetén a null értéket az attribútum hiánya fejezi ki.

**Példa**  
`IsNull([displayName])`  
Igaz értéket ad vissza, ha az attribútum nem szerepel a CS vagy az MV-ban.

---
### <a name="isnullorempty"></a>IsNullOrEmpty
**Leírás:**  
Ha a kifejezés null értékű vagy üres karakterlánc, akkor a IsNullOrEmpty függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsNullOrEmpty(var Expression)`

**Megjegyzéseket tartalmazó**  
Egy attribútum esetében ez igaz értékre értékeli, ha az attribútum hiányzik vagy létezik, de egy üres karakterlánc.  
A függvény inverzének neve IsPresent.

**Példa**  
`IsNullOrEmpty([displayName])`  
Igaz értéket ad vissza, ha az attribútum nincs jelen, vagy üres karakterlánc a CS vagy az MV.

---
### <a name="isnumeric"></a>IsNumeric
**Leírás:**  
A IsNumeric függvény egy logikai értéket ad vissza, amely azt jelzi, hogy egy kifejezés kiértékelhető-e szám típusúként.

**Szintaxis**  
`bool IsNumeric(var Expression)`

**Megjegyzéseket tartalmazó**  
Annak megállapítására szolgál, hogy a CNum () sikeres lehet-e a kifejezés értelmezéséhez.

---
### <a name="isstring"></a>IsString
**Leírás:**  
Ha a kifejezés kiértékelhető karakterlánc típusúra, akkor a IsString függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsString(var expression)`

**Megjegyzéseket tartalmazó**  
Annak megállapítására szolgál, hogy a CStr () sikeres lehet-e a kifejezés értelmezéséhez.

---
### <a name="ispresent"></a>IsPresent
**Leírás:**  
Ha a kifejezés olyan karakterláncot ad vissza, amely nem null értékű, és nem üres, akkor a IsPresent függvény Igaz értéket ad vissza.

**Szintaxis**  
`bool IsPresent(var expression)`

**Megjegyzéseket tartalmazó**  
A függvény inverzének neve IsNullOrEmpty.

**Példa**  
`Switch(IsPresent([directManager]),[directManager], IsPresent([skiplevelManager]),[skiplevelManager], IsPresent([director]),[director])`

---
### <a name="item"></a>Elem
**Leírás:**  
Az Item függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.

**Szintaxis**  
`var Item(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a Többértékű karakterlánc egyik elemébe.

**Megjegyzéseket tartalmazó**  
Az Item függvény a tartalmaz függvénnyel együtt használható, mivel az utóbbi függvény az indexet a többértékű attribútum egy elemébe adja vissza.

Hibát jelez, ha az index tartományon kívül esik.

**Példa**  
`Mid(Item([proxyAddresses],Contains([proxyAddresses], "SMTP:")),6)`  
Az elsődleges e-mail-címet adja vissza.

---
### <a name="itemornull"></a>ItemOrNull
**Leírás:**  
A ItemOrNull függvény egy elemet ad vissza egy többértékű karakterlánc/attribútumból.

**Szintaxis**  
`var ItemOrNull(mvstr attribute, num index)`

* attribútum: többértékű attribútum
* index: index a Többértékű karakterlánc egyik elemébe.

**Megjegyzéseket tartalmazó**  
A ItemOrNull függvény a tartalmaz függvénnyel együtt használható, mivel az utóbbi függvény az indexet a többértékű attribútum egy elemébe adja vissza.

Ha az index tartományon kívül esik, a függvény Null értéket ad vissza.

---
### <a name="join"></a>Csatlakozás
**Leírás:**  
Az illesztési függvény többértékű karakterláncot használ, és egy egyértékű karakterláncot ad vissza, amely az egyes elemek között beszúrt megadott elválasztó karakter.

**Szintaxis**  
`str Join(mvstr attribute)`  
`str Join(mvstr attribute, str Delimiter)`

* attribútum: a egyesíteni kívánt karakterláncokat tartalmazó többértékű attribútum.
* elválasztó: bármely karakterlánc, amely a visszaadott karakterláncban található alsztringek elkülönítésére szolgál. Ha nincs megadva, a rendszer a szóköz karaktert ("") használja. Ha a határolójel nulla hosszúságú karakterlánc ("") vagy semmi sem, a lista minden eleme elválasztó karakterrel van összefűzve.

**Megjegyzések**  
A JOIN és a Split függvények között paritás van. Az illesztési függvény karakterláncok tömbjét veszi fel, és egy elválasztó sztringet használva összekapcsolja őket egyetlen sztring visszaadásához. A felosztott függvény egy karakterláncot fogad el, és elválasztja azt a határolón, hogy sztringek tömbjét állítsa vissza. Azonban a fő különbség az, hogy az illesztés bármely elválasztó karakterlánccal összefűzheti a karakterláncokat, a felosztás csak egyetlen karakterből álló határolójel használatával lehet különálló sztringeket használni.

**Példa**  
`Join([proxyAddresses],",")`  
Visszatérési érték: " SMTP:john.doe@contoso.com , smtp:jd@contoso.com "

---
### <a name="lcase"></a>LCase
**Leírás:**  
A LCase függvény egy karakterlánc összes karakterét kisbetűvé alakítja.

**Szintaxis**  
`str LCase(str value)`

**Példa**  
`LCase("TeSt")`  
A "test" értéket adja vissza.

---
### <a name="left"></a>Bal
**Leírás:**  
A Left függvény egy karakterlánctól balra megadott számú karaktert ad vissza.

**Szintaxis**  
`str Left(str string, num NumChars)`

* string: az a karakterlánc, amelyből karaktereket kell visszaadni
* NumChars: a sztring elejétől (balra) visszaadott karakterek számát azonosító szám.

**Megjegyzéseket tartalmazó**  
A karakterlánc első numChars karaktert tartalmazó karakterlánc:

* Ha a numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a sztring kevesebb karaktert tartalmaz a numChars megadott számnál, akkor a rendszer a karakterlánctal megegyező karakterláncot (azaz az 1. paraméterben szereplő összes karaktert tartalmazza) adja vissza.

**Példa**  
`Left("John Doe", 3)`  
A "Joh" értéket adja vissza.

---
### <a name="len"></a>Len
**Leírás:**  
A len függvény egy karakterláncban szereplő karakterek számát adja vissza.

**Szintaxis**  
`num Len(str value)`

**Példa**  
`Len("John Doe")`  
8 értéket ad vissza

---
### <a name="ltrim"></a>LTrim
**Leírás:**  
A LTrim függvény eltávolítja a kezdő szóközöket egy karakterláncból.

**Szintaxis**  
`str LTrim(str value)`

**Példa**  
`LTrim(" Test ")`  
A "teszt" értéket adja vissza.

---
### <a name="mid"></a>Közepes
**Leírás:**  
A Mid függvény megadott számú karaktert ad vissza egy karakterlánc megadott pozíciójában.

**Szintaxis**  
`str Mid(str string, num start, num NumChars)`

* string: az a karakterlánc, amelyből karaktereket kell visszaadni
* indítás: a karakterláncban szereplő kezdő pozíciót azonosító szám, amely a karakterek visszaküldését eredményezi
* NumChars: a karakterláncban a pozícióból visszaadni kívánt karakterek számának azonosítása.

**Megjegyzéseket tartalmazó**  
A pozíciótól kezdődő numChars-karakterek visszaadása karakterláncban.  
A numChars karaktert tartalmazó karakterlánc a pozíció kezdete karakterláncban:

* Ha a numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
* Ha a Start > a karakterlánc hosszát, adja vissza a bemeneti karakterláncot.
* Ha a Start <= 0, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a karakterláncból nem maradnak numChar karakterek a pozícióból, a rendszer annyi karaktert ad vissza, amennyit csak lehet.

**Példa**  
`Mid("John Doe", 3, 5)`  
A "HN do" értéket adja vissza.

`Mid("John Doe", 6, 999)`  
A "DOE" értéket adja vissza.

---
### <a name="now"></a>Now
**Leírás:**  
A Now függvény egy DateTime értéket ad vissza, amely megadja az aktuális dátumot és időpontot a számítógép rendszerdátumának és időpontjának megfelelően.

**Szintaxis**  
`dt Now()`

---
### <a name="numfromdate"></a>NumFromDate
**Leírás:**  
A NumFromDate függvény egy dátumot AD vissza az Active Directory dátumformátumban.

**Szintaxis**  
`num NumFromDate(dt value)`

**Példa**  
`NumFromDate(CDate("2012-01-01 23:00:00"))`  
129699324000000000 értéket ad vissza

---
### <a name="padleft"></a>PadLeft
**Leírás:**  
A PadLeft függvény bal oldali kitöltési karakterrel a megadott hosszúságú karakterláncot adja.

**Szintaxis**  
`str PadLeft(str string, num length, str padCharacter)`

* string: a pad karakterlánca.
* length (hossz): egy egész szám, amely a kívánt hosszúságú karakterláncot jelöli.
* padCharacter: egyetlen karakterből álló karakterlánc, amely a pad karakterként használható

**Megjegyzéseket tartalmazó**

* Ha a karakterlánc hossza kisebb a hosszúságnál, akkor a rendszer ismételten hozzáfűzi a padCharacter a karakterlánc elejéhez (balra), amíg a hossza nem egyenlő hosszúságú.
* A PadCharacter lehet szóköz karakter, de nem lehet null értékű.
* Ha a karakterlánc hossza egyenlő vagy nagyobb, mint a hossz, a karakterláncot a rendszer változatlanul adja vissza.
* Ha a karakterlánc hossza nagyobb vagy egyenlő, akkor a rendszer a karakterlánctal megegyező karakterláncot adja vissza.
* Ha a karakterlánc hossza kisebb a hosszúságnál, a rendszer a kívánt hosszúságú karakterláncot adja vissza, amely egy padCharacter rendelkező Sztringet tartalmaz.
* Ha a karakterlánc null értékű, a függvény üres karakterláncot ad vissza.

**Példa**  
`PadLeft("User", 10, "0")`  
A "000000User" értéket adja vissza.

---
### <a name="padright"></a>PadRight
**Leírás:**  
A PadRight függvény jobb gombbal egy karakterláncot adott hosszúságú, egy megadott kitöltési karakter használatával.

**Szintaxis**  
`str PadRight(str string, num length, str padCharacter)`

* string: a pad karakterlánca.
* length (hossz): egy egész szám, amely a kívánt hosszúságú karakterláncot jelöli.
* padCharacter: egyetlen karakterből álló karakterlánc, amely a pad karakterként használható

**Megjegyzéseket tartalmazó**

* Ha a karakterlánc hossza kisebb a hosszúságnál, akkor a rendszer ismételten hozzáfűzi a padCharacter a karakterlánc végéhez (jobbra), amíg a hossza nem egyenlő hosszúságú.
* a padCharacter lehet szóköz karakter, de nem lehet null értékű.
* Ha a karakterlánc hossza egyenlő vagy nagyobb, mint a hossz, a karakterláncot a rendszer változatlanul adja vissza.
* Ha a karakterlánc hossza nagyobb vagy egyenlő, akkor a rendszer a karakterlánctal megegyező karakterláncot adja vissza.
* Ha a karakterlánc hossza kisebb a hosszúságnál, a rendszer a kívánt hosszúságú karakterláncot adja vissza, amely egy padCharacter rendelkező Sztringet tartalmaz.
* Ha a karakterlánc null értékű, a függvény üres karakterláncot ad vissza.

**Példa**  
`PadRight("User", 10, "0")`  
A "User000000" értéket adja vissza.

---
### <a name="pcase"></a>PCase
**Leírás:**  
A PCase függvény minden szóközzel tagolt szó első karakterét átalakítja a karakterláncból a nagybetűvé, és az összes többi karakter is kisbetűvé vált.

**Szintaxis**  
`String PCase(string)`

**Megjegyzéseket tartalmazó**

* Ez a függvény jelenleg nem biztosít megfelelő burkolatot egy teljesen nagybetűvel, például egy betűszóval rendelkező szó átalakításához.

**Példa**  
`PCase("TEsT")`  
A "test" értéket adja vissza.

`PCase(LCase("TEST"))`  
A "teszt" értéket adja vissza.

---
### <a name="randomnum"></a>RandomNum
**Leírás:**  
A RandomNum függvény egy véletlenszerű számot ad vissza egy megadott intervallum között.

**Szintaxis**  
`num RandomNum(num start, num end)`

* indítás: a generált véletlenszerű érték alsó határértékét azonosító szám
* End: a generált véletlenszerű érték felső korlátját azonosító szám

**Példa**  
`Random(100,999)`  
734 értéket adhat vissza.

---
### <a name="removeduplicates"></a>RemoveDuplicates
**Leírás:**  
A RemoveDuplicates függvény többértékű karakterláncot használ, és minden érték egyedi.

**Szintaxis**  
`mvstr RemoveDuplicates(mvstr attribute)`

**Példa**  
`RemoveDuplicates([proxyAddresses])`  
Egy megtisztított proxyAddress attribútumot ad vissza, amelyben az összes duplikált érték el lett távolítva.

---
### <a name="replace"></a>Csere
**Leírás:**  
A Replace függvény egy sztring összes előfordulását lecseréli egy másik karakterláncra.

**Szintaxis**  
`str Replace(str string, str OldValue, str NewValue)`

* string: A értékeit helyettesítő karakterlánc.
* OldValue: a keresendő karakterlánc.
* NewValue: a helyére felváltott karakterlánc.

**Megjegyzéseket tartalmazó**  
A függvény a következő speciális monikereket ismeri fel:

* \n – új sor
* \r – kocsivissza
* \t – lap

**Példa**  
`Replace([address],"\r\n",", ")`  
Lecseréli a CRLF vesszővel és szóközzel, és "egy Microsoft Way, Redmond, WA, USA"-t eredményezhet

---
### <a name="replacechars"></a>ReplaceChars
**Leírás:**  
A ReplaceChars függvény lecseréli a ReplacePattern karakterláncban található összes karakter előfordulását.

**Szintaxis**  
`str ReplaceChars(str string, str ReplacePattern)`

* string: A karaktereit lecserélő karakterlánc.
* ReplacePattern: olyan karakterlánc, amely a lecserélni kívánt karaktereket tartalmazó szótárat tartalmaz.

A formátum a következő: {source1}: {target1}, {source2}: {TARGET2}, {sourceN}, {targetN}, ahol a forrás az a karakter, amely a helyére írja a karakterláncot.

**Megjegyzéseket tartalmazó**

* A függvény a definiált források minden előfordulását végrehajtja, és azokat a célokkal helyettesíti.
* A forrásnak pontosan egy (Unicode) karakternek kell lennie.
* A forrás nem lehet üres vagy hosszabb egy karakternél (elemzési hiba).
* A cél több karakterből állhat, például √: OE, β: SS.
* A cél üres is lehet, ami azt jelzi, hogy a karaktert el kell távolítani.
* A forrás megkülönbözteti a kis-és nagybetűket, és pontosan egyeznie kell.
* A (z), (vessző) és a: (kettőspont) foglalt karakter, ezért ez a függvény nem cserélhető le.
* A rendszer figyelmen kívül hagyja a szóközöket és más fehér karaktereket a ReplacePattern karakterláncban.

**Példa**  
`%ReplaceString% = ’:,Å:A,Ä:A,Ö:O,å:a,ä:a,ö,o`

`ReplaceChars("Räksmörgås",%ReplaceString%)`  
Raksmorgas visszaadása

`ReplaceChars("O’Neil",%ReplaceString%)`  
A "ONeil" értéket adja vissza, az egyetlen kullancsot a rendszer eltávolítja.

---
### <a name="right"></a>Jobb
**Leírás:**  
A RIGHT függvény egy karakterlánc jobb oldali (záró) karakterét adja vissza megadott számú karakterből.

**Szintaxis**  
`str Right(str string, num NumChars)`

* string: az a karakterlánc, amelyből karaktereket kell visszaadni
* NumChars: a karakterlánc végétől (jobbra) visszaadni kívánt karakterek számának azonosítása.

**Megjegyzéseket tartalmazó**  
A NumChars karaktereket a rendszer a karakterlánc utolsó helyén adja vissza.

A karakterlánc utolsó numChars karaktert tartalmazó karakterlánc:

* Ha a numChars = 0, üres karakterláncot ad vissza.
* Ha a numChars < 0 értéket, adja vissza a bemeneti karakterláncot.
* Ha a karakterlánc null értékű, üres karakterláncot ad vissza.

Ha a karakterlánc a NumChars megadott számnál kevesebb karaktert tartalmaz, a rendszer a karakterlánctal megegyező karakterláncot adja vissza.

**Példa**  
`Right("John Doe", 3)`  
A "DOE" értéket adja vissza.

---
### <a name="rtrim"></a>RTrim
**Leírás:**  
A RTrim függvény eltávolítja a szóközöket a sztringből.

**Szintaxis**  
`str RTrim(str value)`

**Példa**  
`RTrim(" Test ")`  
A "test" értéket adja vissza.

---
### <a name="select"></a>Válassza ezt:
**Leírás:**  
Egy többértékű attribútum (vagy egy kifejezés kimenete) összes értékének feldolgozása a megadott függvény alapján.

**Szintaxis**  
`mvattr Select(variable item, mvattr attribute, func function)`  
`mvattr Select(variable item, exp expression, func function)`

* Item: a többértékű attribútum egy elemét jelöli.
* attribútum: a többértékű attribútum
* kifejezés: értékek gyűjteményét visszaadó kifejezés
* feltétel: bármely olyan függvény, amely képes feldolgozni az attribútum egy tételét

**Példák:**  
`Select($item,[otherPhone],Replace($item,"-",""))`  
A többszörös értékű attribútum otherPhone összes értékének visszaadása a kötőjelek (-) után.

---
### <a name="split"></a>Felosztás
**Leírás:**  
A Split függvény egy karakterláncot választ el egy elválasztóval, és többértékű karakterláncot tesz lehetővé.

**Szintaxis**  
`mvstr Split(str value, str delimiter)`  
`mvstr Split(str value, str delimiter, num limit)`

* érték: a karakterlánc, amely elválasztó karakterből áll.
* elválasztó: a határolójelként használandó egyetlen karakter.
* korlát: a visszaadható értékek maximális száma.

**Példa**  
`Split("SMTP:john.doe@contoso.com,smtp:jd@contoso.com",",")`  
Egy többértékű karakterláncot ad vissza, amelynek 2 eleme hasznos a proxyAddress attribútumhoz.

---
### <a name="stringfromguid"></a>StringFromGuid
**Leírás:**  
A StringFromGuid függvény bináris GUID-azonosítót használ, és átalakítja karakterlánccá

**Szintaxis**  
`str StringFromGuid(bin GUID)`

---
### <a name="stringfromsid"></a>StringFromSid
**Leírás:**  
A StringFromSid függvény egy olyan byte tömböt alakít át, amely biztonsági azonosítót tartalmaz egy karakterláncra.

**Szintaxis**  
`str StringFromSid(bin ObjectSID)`  

---
### <a name="switch"></a>Kapcsoló (switch)
**Leírás:**  
A Switch függvény egyetlen érték visszaadására szolgál a kiértékelt feltételek alapján.

**Szintaxis**  
`var Switch(exp expr1, var value1[, exp expr2, var value … [, exp expr, var valueN]])`

* kifejezés: a kiértékelni kívánt Variant kifejezés.
* érték: a visszaadott érték, ha a megfelelő kifejezés igaz.

**Megjegyzéseket tartalmazó**  
A Switch függvény argumentumok listája kifejezésből és értékből álló párokat tartalmaz. A kifejezések kiértékelése balról jobbra történik, és az első kifejezéshez társított érték igaz értéket ad vissza. Ha a részek nem megfelelően vannak párosítva, futásidejű hiba történik.

Ha például a Kif1 értéke igaz, a Switch visszaadja a érték1 értéket. Ha a kifejezés-1 hamis, de a kifejezés-2 értéke igaz, a switch a-2 értéket adja vissza, és így tovább.

A kapcsoló a következőket adja vissza:

* A kifejezések egyike sem igaz.
* Az első True kifejezésnek van egy NULL értékű megfelelő értéke.

A kapcsoló kiértékeli az összes kifejezést, annak ellenére, hogy csak az egyiket adja vissza. Emiatt érdemes figyelni a nemkívánatos mellékhatásokat. Ha például egy kifejezés kiértékelése nullával történő osztást eredményez, hiba történik.

Az érték lehet a Error függvény is, amely egyéni karakterláncot ad vissza.

**Példa**  
`Switch([city] = "London", "English", [city] = "Rome", "Italian", [city] = "Paris", "French", True, Error("Unknown city"))`  
Az egyes főbb városokban beszélt nyelvet adja vissza, ellenkező esetben hibát ad vissza.

---
### <a name="trim"></a>Trim
**Leírás:**  
A Trim függvény eltávolítja a kezdő és záró szóközöket egy karakterláncból.

**Szintaxis**  
`str Trim(str value)`  

**Példa**  
`Trim(" Test ")`  
A "test" értéket adja vissza.

`Trim([proxyAddresses])`  
Eltávolítja a kezdő és záró szóközöket a proxyAddress attribútum minden értékéhez.

---
### <a name="ucase"></a>UCase
**Leírás:**  
A UCase függvény a sztring összes karakterét nagybetűvé alakítja.

**Szintaxis**  
`str UCase(str string)`

**Példa**  
`UCase("TeSt")`  
A "TEST" értéket adja vissza.

---
### <a name="where"></a>Ahol

**Leírás:**  
Egy többértékű attribútum (vagy egy kifejezés kimenete) értékének egy részhalmazát adja vissza adott feltétel alapján.

**Szintaxis**  
`mvattr Where(variable item, mvattr attribute, exp condition)`  
`mvattr Where(variable item, exp expression, exp condition)`  
* Item: a többértékű attribútum egy elemét jelöli.
* attribútum: a többértékű attribútum
* feltétel: bármely kifejezés, amelynek kiértékelése igaz vagy hamis lehet
* kifejezés: értékek gyűjteményét visszaadó kifejezés

**Példa**  
`Where($item,[userCertificate],CertNotAfter($item)>Now())`  
A nem elévült többértékű attribútum userCertificate visszaadása.

---
### <a name="with"></a>With
**Leírás:**  
A with függvény lehetővé teszi egy összetett kifejezés egyszerűsítését egy változó használatával, amely egy vagy több időpontot jelenít meg a komplex kifejezésben.

**Szintaxis**
`With(var variable, exp subExpression, exp complexExpression)`  
* változó: az alkifejezést jelöli.
* alkifejezés: a változó által jelölt alkifejezés.
* complexExpression: összetett kifejezés.

**Példa**  
`With($unExpiredCerts,Where($item,[userCertificate],CertNotAfter($item)>Now()),IIF(Count($unExpiredCerts)>0,$unExpiredCerts,NULL))`  
Funkcionálisan egyenértékű a következővel:  
`IIF (Count(Where($item,[userCertificate],CertNotAfter($item)>Now()))>0, Where($item,[userCertificate],CertNotAfter($item)>Now()),NULL)`  
Amely csak a nem lejárt tanúsítvány-értékeket adja vissza a userCertificate attribútumban.


---
### <a name="word"></a>Word
**Leírás:**  
A Word függvény egy karakterláncon belül található szót ad vissza, amely a használni kívánt határolójeleket és a visszaadni kívánt szó paramétereit írja le.

**Szintaxis**  
`str Word(str string, num WordNumber, str delimiters)`

* string: az a karakterlánc, amelyből a szót vissza kell adni.
* WordNumber: az a szám, amelyből a szót vissza kell adni.
* határolójelek: a szavak azonosítására szolgáló elválasztó karakter (eke) t jelölő sztring

**Megjegyzéseket tartalmazó**  
A rendszer az elválasztó karakterek egyikével elválasztott karakterláncban szereplő összes karakterláncot szavakként azonosítja:

* Ha a szám < 1, üres karakterláncot ad vissza.
* Ha a sztring null, üres karakterláncot ad vissza.

Ha a karakterlánc kevesebb, mint szám szót tartalmaz, vagy a sztring nem tartalmaz határolójelekkel azonosított szavakat, akkor a rendszer üres karakterláncot ad vissza.

**Példa**  
`Word("The quick brown fox",3," ")`  
A "barna" értéket adja vissza.

`Word("This,string!has&many separators",3,",!&#")`  
Visszatérési érték: "has"

## <a name="additional-resources"></a>További források
* [A deklaratív üzembe helyezési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect Sync: szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
